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
                // इमेजला तुमच्या युजरनेमसह टॅग करा
                sh "docker build -t ${DOCKER_HUB_USER}/${IMAGE_NAME}:${env.BUILD_NUMBER} ."
            }
        }

        // --- ही स्टेज इथे ॲड करा ---
        stage("push") {
            steps {
                script {
                    // 'docker-hub-creds' हा तुम्ही तयार केलेला ID आहे
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                        sh "echo \$PASS | docker login -u \$USER --password-stdin"
                        sh "docker push ${DOCKER_HUB_USER}/${IMAGE_NAME}:${env.BUILD_NUMBER}"
                    }
                }
            }
        }

        stage("deploy") {
            steps {
                sh "docker stop my-nginx-container || true"
                sh "docker rm my-nginx-container || true"
                // आता Docker Hub वरून पुश केलेली इमेज वापरून कंटेनर रन करा
                sh "docker run -d -p 80:80 --name my-nginx-container ${DOCKER_HUB_USER}/${IMAGE_NAME}:${env.BUILD_NUMBER}"
            }
        }
    }
    
    // जागा वाचवण्यासाठी लोकल इमेजेस डिलीट करा
    post {
        always {
            sh "docker rmi ${DOCKER_HUB_USER}/${IMAGE_NAME}:${env.BUILD_NUMBER} || true"
        }
    }
}