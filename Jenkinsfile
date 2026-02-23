pipeline {
    agent any

    environment {
        IMAGE_NAME = "jarvisadr/literature-frontend"
        IMAGE_TAG = "latest"
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build image sesuai Dockerfile yang ada
                    docker.build("${IMAGE_NAME}:${IMAGE_TAG}")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                // Pastikan credentialsId sesuai dengan yang Anda buat di Jenkins
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
                script {
                    // Menjalankan deployment otomatis menggunakan Docker Compose
                    // Perintah ini akan mem-build ulang dan deploy container yang didefinisikan di docker-compose.yml
                    sh "docker-compose up -d --build"
                }
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
