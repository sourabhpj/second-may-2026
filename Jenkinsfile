pipeline {
    agent any
    stages {
        stage("checkout") {
            steps {
                git scm
            }
        }
        stage("build") {
            steps {
                sh "docker build -t my-nginx-image ."
            }
        }
        stage("deploy") {
            steps {
                sh "docker stop my-nginx-container || true"
                sh "docker rm my-nginx-container || true"
                sh "docker run -d -p 80:80 --name my-nginx-container my-nginx-image"
            }
        }
    }
}