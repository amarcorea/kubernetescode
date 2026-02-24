node {
    // Las variables en Scripted Pipeline se declaran así:
    def CALL_JOB_NAME = 'deployment-cd'
    def DOCKER_IMAGE_NAME = 'amarcorea/appdeployment'
    def DOCKERHuB_CREDS = 'dockerhubcreds'
    def app

    stage('Clone repository') {
        checkout scm
    }

    stage('Build image') {
        // Usamos interpolación con comillas dobles para la variable
        app = docker.build("${DOCKER_IMAGE_NAME}")
    }

    stage('Test image') {
        app.inside {
            sh 'echo "Tests passed"'
        }
    }

    stage('Push image') {
        // El ID de las credenciales va directo o como variable
        docker.withRegistry('https://registry.hub.docker.com', DOCKERHuB_CREDS) {
            app.push("${env.BUILD_NUMBER}")
            app.push("latest") // Tip: Siempre es bueno tener un tag latest
        }
    }
    
    stage('Trigger ManifestUpdate') {
        echo "triggering Continuos deployment"
        // Llamamos al otro job pasando el tag
        build job: CALL_JOB_NAME, parameters: [string(name: 'DOCKERTAG', value: "${env.BUILD_NUMBER}")]
    }
}
