pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "ouabou2003/my-springboot-app"
        DOCKERHUB_CREDENTIALS_ID = "ouabou-dockerhub"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: "${env.BRANCH_NAME}", url: 'https://github.com/yassineouabou/jenkins.git'
            }
        }

        stage("Test") {
            when {
                anyOf {
                    branch pattern: "feature/.*", comparator: "REGEXP"
                    branch 'dev'
                    branch 'prod'
                }
            }
            steps {
                sh 'mvn clean test'
            }
        }

        stage("Build") {
            when {
                anyOf {
                    branch pattern: "feature/.*", comparator: "REGEXP"
                    branch 'dev'
                    branch 'prod'
                }
            }
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Build Docker Image') {
            when {
                branch 'prod' // uniquement dans la branche prod
            }
            steps {
                sh "docker build -t ${DOCKER_IMAGE}:latest ."
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
                    sh "docker push ${DOCKER_IMAGE}:latest"
                }
            }
        }
    }
}
