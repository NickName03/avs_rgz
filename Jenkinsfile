pipeline {
    agent any

    environment {
        IMAGE_NAME = "my-application"
        REGISTRY = "my-docker-registry.local"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/NickName03/avs_rgz'
            }
        }

         stage('Get Docker Daemon IP') {
            steps {
               script {
                    def dockerDaemonIP = sh(script: 'docker -H tcp://docker-daemon:2375 inspect docker-daemon | grep IPAddress | cut -d\\" -f4', returnStdout: true).trim()
                    env.DOCKER_HOST = "tcp://${dockerDaemonIP}:2375"
                }
            }
        }

        stage('Build Docker Image') {
           steps {
                script {
                    sleep 10 // Wait for 10 seconds
                }
                sh '''
                docker -H $DOCKER_HOST build -t $IMAGE_NAME:$BUILD_NUMBER .
                '''
            }
        }

        stage('Push Docker Image') {
            steps {
                sh '''
                docker -H $DOCKER_HOST tag $IMAGE_NAME:$BUILD_NUMBER $REGISTRY/$IMAGE_NAME:$BUILD_NUMBER
                docker -H $DOCKER_HOST push $REGISTRY/$IMAGE_NAME:$BUILD_NUMBER
                '''
            }
        }

        stage('Deploy Container') {
            steps {
               sh '''
                docker -H $DOCKER_HOST stop $IMAGE_NAME || true
                docker -H $DOCKER_HOST rm $IMAGE_NAME || true
                docker -H $DOCKER_HOST run -d --name $IMAGE_NAME -p 80:80 $REGISTRY/$IMAGE_NAME:$BUILD_NUMBER
                '''
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
