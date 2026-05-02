pipeline {
    agent any
    
    environment {
        DOCKER_HUB_USER = "sourabhpj94"
        IMAGE_NAME = "my-nginx-image"
    }

    stages {
        stage("checkout") {
            steps {
                checkout scm
            }
        }

        stage("build") {
            steps {
                // इमेजला बिल्ड नंबरसह टॅग करा
                sh "docker build -t ${DOCKER_HUB_USER}/${IMAGE_NAME}:${env.BUILD_NUMBER} ."
                // 'latest' टॅग पण द्या म्हणजे YAML फाईलमध्ये सारखे बदल करावे लागणार नाहीत
                sh "docker build -t ${DOCKER_HUB_USER}/${IMAGE_NAME}:latest ."
            }
        }

        stage("push") {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                        sh "echo \$PASS | docker login -u \$USER --password-stdin"
                        sh "docker push ${DOCKER_HUB_USER}/${IMAGE_NAME}:${env.BUILD_NUMBER}"
                        sh "docker push ${DOCKER_HUB_USER}/${IMAGE_NAME}:latest"
                    }
                }
            }
        }

        // --- Kubernetes Deployment Stage ---
        stage('Deploy to Kubernetes') {
            steps {
                script {
                      // withEnv साठी नेहमी (["KEY=VALUE"]) हा फॉरमॅट वापरा
                    withEnv(["KUBECONFIG=/home/ubuntu/.kube/config"]) {
                        sh "kubectl apply -f ${WORKSPACE}/kubernetes/deployment.yaml"
                        sh "kubectl apply -f ${WORKSPACE}/kubernetes/service.yaml"
                        sh "kubectl rollout restart deployment/my-nginx-deployment"

                }
            }
        }
    }
    
    post {
        always {
            // क्लीनअप: लोकल इमेजेस डिलीट करा जेणेकरून जागा वाचेल
            sh "docker rmi ${DOCKER_HUB_USER}/${IMAGE_NAME}:${env.BUILD_NUMBER} || true"
            sh "docker rmi ${DOCKER_HUB_USER}/${IMAGE_NAME}:latest || true"
        }
    }
}