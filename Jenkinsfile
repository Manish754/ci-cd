def image
pipeline {
    environment {
        registry = "manish754/cicd"
        registryCredential = 'Dokcer_hub'
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
        stage("Push to docker_hub") {
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
                     sshagent (['dev-prod']){
                     sh "ssh -o StrictHostKeyChecking=no ubuntu@3.22.164.90 ${dockerRun}"
                     
                    }
                }
            }
            
        }
          
    }

}
