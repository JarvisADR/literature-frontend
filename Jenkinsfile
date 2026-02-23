pipeline {
    agent any

    environment {
        IMAGE_NAME = "jarvisadr/literature-frontend"
        IMAGE_TAG = "latest"
        DOCKER_HOST = "unix:///var/run/docker.sock"
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                // Menggunakan sh agar Jenkins memanggil Docker host melalui socket
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-repo-jarvis', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh """
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push ${IMAGE_NAME}:${IMAGE_TAG}
                    """
                }
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
            sh "cat nginx.conf > /tmp/nginx.conf"
            sh "cat /tmp/nginx.conf" // Untuk memastikan isinya benar di log Jenkins
            sh "docker compose up -d --build --force-recreate"
            }
        }
    }

    post {
        success { echo "✅ Deployment Berhasil" }
        failure { echo "❌ Deployment Gagal" }
        always {
            sh "docker system prune -f"
        }
    }
}
