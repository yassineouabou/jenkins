pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "ouabou2003/my-springboot-app"
        DOCKERHUB_CREDENTIALS_ID = "ouabou-dockerhub"
        KUBECONFIG = "${env.WORKSPACE}/.kube/config"
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/yassineouabou/jenkins.git'
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
                branch 'master'
            }
            steps {
                sh "docker build -t ${DOCKER_IMAGE}:v2 ."
            }
        }

        stage('Push Docker Image') {
            when {
                branch 'master'
            }
            steps {
                withCredentials([usernamePassword(
                    credentialsId: "${DOCKERHUB_CREDENTIALS_ID}",
                    usernameVariable: 'DOCKER_USERNAME',
                    passwordVariable: 'DOCKER_PASSWORD'
                )]) {
                    sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin'
                    sh "docker push ${DOCKER_IMAGE}:v2"
                }
            }
        }

        stage('Setup kubectl') {
            when {
                branch 'master'
            }
            steps {
                withCredentials([string(credentialsId: 'k8s-jenkins-token', variable: 'TOKEN')]) {
                    sh '''
                        mkdir -p ~/.kube
                        kubectl config set-cluster my-cluster --server=https://host.docker.internal:64239 --insecure-skip-tls-verify=true
                        kubectl config set-credentials jenkins-sa --token=$TOKEN
                        kubectl config set-context jenkins-context --cluster=my-cluster --user=jenkins-sa
                        kubectl config use-context jenkins-context
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            when {
                branch 'master'
            }
            steps {
                sh 'kubectl config view'
                sh 'kubectl apply -f deploymentService.yaml'
            }
        }
    }
}
