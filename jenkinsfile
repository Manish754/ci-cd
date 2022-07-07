def image
pipeline {
    environment {
        registry = "22119697/pipline"
        registryCredential = 'docker_hub'
        dockerImage = ''
    }
    agent any

    stages{
        
        stage("Build Image") {

            steps {
                script {
                     image = registry + ":${env.BUILD_NUMBER}"
                     println ("${image}")
                     dockerImage = docker.build image
                    
                }
            }

        

        }

        stage("Testing - running in Jenkins Node") {

            steps {
                sh "docker run -d --name ${JOB_NAME} -p 5000:5000 ${image}"
            }
        }
        stage('Cleaning up') {
             steps {
                 sh "docker rm -f ${JOB_NAME}"
               }
        }
        stage("Push to Dockerhub") {
            steps {
                script {
                    docker.withRegistry('', registryCredential ) {
                        dockerImage.push()
                    }
                }
            }
        }
        stage("running on dev-server") {
            steps{
                script{
                     def dockerRun = "docker run -d --name ${JOB_NAME} -p 5000:5000 ${image}"
                     sshagent (['dev-server']){
                     sh "ssh -o StrictHostKeyChecking=no ubuntu@18.118.85.235 ${dockerRun}"
                     
                    }
                }
            }
            
        }
          
    }

}
