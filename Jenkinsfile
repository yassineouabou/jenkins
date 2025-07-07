pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "yassineouabou/my-springboot-app"
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/yassineouabou/jenkins.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    def appImage = docker.build("${DOCKER_IMAGE}:latest")
                }
            }
        }
    }

    post {
        success {
            echo 'Image Docker créée avec succès.'
        }
        failure {
            echo 'Échec de la création de l’image Docker.'
        }
    }
}
