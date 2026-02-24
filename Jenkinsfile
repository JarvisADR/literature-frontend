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
                // 1. Salin nginx.conf dari workspace Jenkins ke folder jembatan di Host Ubuntu
                // Kita gunakan path absolut yang sudah kita verifikasi tadi
                sh "cat nginx.conf > /var/lib/docker/volumes/jenkins_home/_data/workspace/literature-frontend_main/nginx-runtime-config/default.conf"
                
                // 2. Beri izin baca agar container Nginx bisa mengaksesnya
                sh "chmod 644 /var/lib/docker/volumes/jenkins_home/_data/workspace/literature-frontend_main/nginx-runtime-config/default.conf"
                
                // 3. Jalankan Docker Compose
                // Menggunakan --force-recreate agar Nginx memuat volume yang baru
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
