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
                sh 'docker version'
                sh 'docker build -t yassineouabou/my-springboot-app:latest .'
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
