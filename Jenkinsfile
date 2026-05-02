pipeline {
    agent any

    environment {
        DOCKER_HUB_USER = 'sourabhpj94'
        IMAGE_NAME = 'my-nginx-image'
    }

    stages {
        stage('Checkout Source') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${DOCKER_HUB_USER}/${IMAGE_NAME}:${env.BUILD_NUMBER} ."
                sh "docker tag ${DOCKER_HUB_USER}/${IMAGE_NAME}:${env.BUILD_NUMBER} ${DOCKER_HUB_USER}/${IMAGE_NAME}:latest"
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([string(credentialsId: 'docker-hub-creds', variable: 'DOCKER_HUB_PASSWORD')]) {
                    sh "echo \$DOCKER_HUB_PASSWORD | docker login -u ${DOCKER_HUB_USER} --password-stdin"
                    sh "docker push ${DOCKER_HUB_USER}/${IMAGE_NAME}:${env.BUILD_NUMBER}"
                    sh "docker push ${DOCKER_HUB_USER}/${IMAGE_NAME}:latest"
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // KUBECONFIG आणि WORKSPACE दोन्ही सेट केले आहेत जेणेकरून Permission आणि Path एरर येणार नाहीत
                    withEnv(["KUBECONFIG=/home/ubuntu/.kube/config"]) {
                        sh "kubectl apply -f ${WORKSPACE}/kubernetes/deployment.yaml"
                        sh "kubectl apply -f ${WORKSPACE}/kubernetes/service.yaml"
                        sh "kubectl rollout restart deployment/my-nginx-deployment"
                    }
                }
            }
        }
    } // Stages चा शेवट

    post {
        always {
            // बिल्ड पूर्ण झाल्यावर इमेज डिलीट करा जेणेकरून स्टोरेज फुल होणार नाही
            sh "docker rmi ${DOCKER_HUB_USER}/${IMAGE_NAME}:${env.BUILD_NUMBER} || true"
        }
    }
} // Pipeline चा शेवट