pipeline{
    agent {
        label  'build-vm'
    }
    environment{
        IMAGE = "jyotimanab/task-tracker-web"
        TAG =  "${env.BUILD_NUMBER}"
    }

    stages{
        stage('git cehckout'){
            steps{
                git branch: 'main',
                    url: 'https://github.com/zerotoprodevops/task-tracker-web.git'
            }
        }
        stage('build stage'){      
                steps{
                    sh 'docker build -t "$IMAGE:$TAG" .'
                }
        }    
        stage('trivy scan'){
            steps{
                sh 'TMPDIR=//tempdir trivy image --severity HIGH,CRITICAL --exit-code 1 $IMAGE:$TAG'
            }
          } 
        stage('upload docker image to dockerHub'){
            steps{
                withDockerRegistry(credentialsId: 'dockerhub-cred', url: 'https://hub.docker.com/repositories/jyotimanab') {
                    sh """
                        docker push $IMAGE:$TAG
                        docker tag $IMAGE:$TAG $IMAGE:latest
                        docker push $IMAGE:latest
                       """ 
                }
            }
        }  
    }
}