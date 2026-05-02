Pipeline {
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
                    // क्लस्टरवर तुमच्या YAML फाइल्स अप्लाय करणे
                    // खात्री करा की तुमच्या GitHub मध्ये 'kubernetes' नावाचा फोल्डर आहे
                    sh "kubectl apply -f kubernetes/deployment.yaml"
                    sh "kubectl apply -f kubernetes/service.yaml"
                    
                    // इमेज अपडेट झाली आहे हे खात्री करण्यासाठी rollout restart करा
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