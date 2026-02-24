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
                // Jenkins akan otomatis membuat folder ini di dalam workspace-nya
                sh "mkdir -p nginx-runtime-config"
                
                // Gunakan path relatif, Jenkins pasti kenal folder ini
                sh "cat nginx.conf > nginx-runtime-config/default.conf"
                
                // Beri izin agar Docker Host bisa membacanya
                sh "chmod 777 nginx-runtime-config"
                sh "chmod 644 nginx-runtime-config/default.conf"
                
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
