node('docker-executor') {

    withEnv(['DOCKER_CONTEXT=default']) {
        // Definimos las variables como Strings explícitos
        String imageName = 'amarcorea/appdeployment'
        String dockerHubCreds = 'dockerhubcreds'
        String targetJob = 'deployment-cd'
        def app
    
        stage('Clone repository') {
            checkout scm
        }
    
        stage('Build image') {
            // Usamos la variable directamente sin ${} para evitar errores de interpolación
            app = docker.build(imageName)
        }
    
        /*
        stage('Test image') {
            app.inside("-u 1000:1000") { 
                sh 'echo "Tests passed"'
            }
        }
        */
    
        stage('Push image') {
            docker.withRegistry('https://registry.hub.docker.com', dockerHubCreds) {
                // env.BUILD_NUMBER es una variable global, aseguramos que sea String
                app.push("${env.BUILD_NUMBER}")
            }
        }
    
        stage('Trigger ManifestUpdate') {
            build job: targetJob, parameters: [string(name: 'DOCKERIMAGE', value: imageName), string(name: 'DOCKERTAG', value: "${env.BUILD_NUMBER}")]
        }
    }
}
