pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "yassineouabou/my-springboot-app"
        DOCKERHUB_CREDENTIALS_ID = "ouabou-dockerhub"
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/yassineouabou/jenkins.git'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn clean test'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker version'
                sh "docker build -t ${DOCKER_IMAGE}:latest ."
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: "${DOCKERHUB_CREDENTIALS_ID}",
                    usernameVariable: 'DOCKER_USERNAME',
                    passwordVariable: 'DOCKER_PASSWORD'
                )]) {
                    sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin'
                    sh "docker push ${DOCKER_IMAGE}:latest"
                }
            }
        }
    }
}
