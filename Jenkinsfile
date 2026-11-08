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
                withDockerRegistry(credentialsId: 'dockerhub-cred', url: '') {
                    sh """
                        docker push $IMAGE:$TAG
                        docker tag $IMAGE:$TAG $IMAGE:latest
                        docker push $IMAGE:latest
                       """ 
                }
            }
        }  
    }
    post {
        success{
            echo "Buils ${TAG} successful - Image pushed to Docker Hub"
            sh "docker rmi $IMAGE:$TAG $IMAGE:latest || echo 'cleanup: Image not found locally'"
        }
        failure{
            echo "Pipeline Failed: Image $Image:$TAG kept locally for debugging"
        }
    }
}