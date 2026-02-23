pipeline {
    agent any

    environment {
        IMAGE_NAME = "JarvisADR/literature-frontend"
        IMAGE_TAG = "latest"
    }

    stages {
        stage('Checkout Code') {
            steps {
                // Menggunakan repo sesuai instruksi poin 7
                git url: 'https://github.com/sinambela99/literature-frontend', branch: 'main' [cite: 7]
            }
        }

        stage('Fix & Build Image') {
            steps {
                script {
                    // Wajib: Hapus baris 15 yang menyebabkan error build
                    sh "sed -i '15d' Dockerfile"
                    sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
                }
            }
        }

        stage('Push Image') {
            steps {
                withCredentials([usernamePassword(
                credentialsId: 'dockerhub-credentials',
                usernameVariable: 'DOCKER_USER',
                passwordVariable: 'DOCKER_PASS' // Ubah menjadi passwordVariable
            )])
                    sh """
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push ${IMAGE_NAME}:${IMAGE_TAG}
                    """
                }
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                // Wajib menggunakan docker compose sesuai instruksi poin 18
                sh "docker compose up -d" [cite: 18]
            }
        }
    }

    post {
        success {
            echo "Deployment berhasil melalui Docker Compose" [cite: 19]
        }
        failure {
            echo "Deployment gagal, periksa konfigurasi Docker Compose atau Dockerfile"
        }
    }
}
