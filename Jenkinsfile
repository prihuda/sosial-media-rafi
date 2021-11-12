env.DOCKER_REGISTRY = 'prihuda22'
env.DOCKER_IMAGE = 'sosial-media-bp-stag'


pipeline {
    agent any

    stages {
        stage('Hello World') { 
            steps {
                sh "whoami"
            }
        }

        stage('Clone Source Code') {
            steps {
                sh '''
                if [ -d big-project ]
                then
                    ls
                else 
                    git clone https://github.com/prihuda/big-project.git
                fi
                '''
            }
        }

        stage('Docker Build Image') { 
             steps {
                 sh "docker build -t $DOCKER_REGISTRY/$DOCKER_IMAGE:${BUILD_NUMBER} ."
             }
         }
         stage('Push Docker Image') { 
             steps {
                 sh "docker push $DOCKER_REGISTRY/$DOCKER_IMAGE:${BUILD_NUMBER}"
             }
         }

         stage('Deploy Image to Kubernetes') { 
             steps {
                 sh """ sed -i 's;prihuda22/sosial-media-sp3:v2;$DOCKER_REGISTRY/$DOCKER_IMAGE:${BUILD_NUMBER};g' ./big-project/facebook-staging/facebook.yaml """
                 sh "kubectl apply -f ./big-project/staging.json"
                 sh "kubectl apply -f ./big-project/facebook-staging/configmap.yaml"
    	         sh "kubectl apply -f ./big-project/facebook-staging/facebook.yaml"
                 sh "kubectl apply -f ./big-project/ingress-staging/ingress.yaml"
             }
         }

         stage('Delete Image') { 
             steps {
                 sh "docker rmi $DOCKER_REGISTRY/$DOCKER_IMAGE:${BUILD_NUMBER}"
             }
         }

         stage('Cleaning FIle....') { 
             steps {
                 cleanWs()
             }
         }
    }
}