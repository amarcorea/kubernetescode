node {
    def app

    environment{
        CALL_JOB_NAME = 'deployment-cd'
        DOCKER_IMAGE_NAME = 'amarcorea/appdeployment'
        DOCKERHuB_CREDS = 'dockerhubcreds'
    }

    stage('Clone repository') {
      

        checkout scm
    }

    stage('Build image') {
  
       app = docker.build('${DOCKER_IMAGE_NAME}')
    }

    stage('Test image') {
  

        app.inside {
            sh 'echo "Tests passed"'
        }
    }

    stage('Push image') {
        
        docker.withRegistry('https://registry.hub.docker.com', '${DOCKERHuB_CREDS}') {
            app.push("${env.BUILD_NUMBER}")
        }
    }
    
    stage('Trigger ManifestUpdate') {
                echo "triggering Continuos deployment"
                build job: '${CALL_JOB_NAME}', parameters: [string(name: 'DOCKERTAG', value: env.BUILD_NUMBER)]
        }
}
