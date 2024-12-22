pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "my-app"
        DOCKER_CONTAINER = "app"
    }
    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/NickName03/avs_rgz'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t ${DOCKER_IMAGE}:latest .'
            }
        }
        stage('Deploy Container') {
            steps {
                sh '''
                if docker ps -q --filter "name=${DOCKER_CONTAINER}"; then
                    docker stop ${DOCKER_CONTAINER} && docker rm ${DOCKER_CONTAINER}
                fi
                docker run -d --name ${DOCKER_CONTAINER} -p 8081:8081 ${DOCKER_IMAGE}:latest
                '''
            }
        }
    }
    triggers {
        pollSCM('H/2 * * * *') // Проверка изменений каждые 2 минуты
    }
}