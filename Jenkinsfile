pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "ouabou2003/my-springboot-app"
        DOCKERHUB_CREDENTIALS_ID = "ouabou-dockerhub"
        BRANCH_NAME = "${env.BRANCH_NAME}"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: "${BRANCH_NAME}", url: 'https://github.com/yassineouabou/jenkins.git'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn clean test'
            }
        }

        stage('Build Jar') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            when {
                branch 'prod'
            }
            steps {
                sh "docker build -t ${DOCKER_IMAGE}:prod ."
            }
        }

        stage('Push Docker Image') {
            when {
                branch 'prod'
            }
            steps {
                withCredentials([usernamePassword(
                    credentialsId: "${DOCKERHUB_CREDENTIALS_ID}",
                    usernameVariable: 'DOCKER_USERNAME',
                    passwordVariable: 'DOCKER_PASSWORD'
                )]) {
                    sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin'
                    sh "docker push ${DOCKER_IMAGE}:prod"
                }
            }
        }
    }
}
