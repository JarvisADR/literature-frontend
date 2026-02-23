pipeline {
    agent any

    environment {
        IMAGE_NAME = "jarvisadr/literature-frontend"
        IMAGE_TAG = "latest"
    }

    stages {
        stage('Checkout Code') {
            steps {
                // Menggunakan repo sesuai instruksi poin 7
                git url: 'https://github.com/JarvisADR/literature-frontend', branch: 'main' [cite: 7]
            }
        }

        stage('Fix & Build Image') {
            steps {
                script {
                    sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
                }
            }
        }

        stage('Push Image') {
            steps {
                withCredentials([usernamePassword(
                credentialsId: 'dockerhub-credentials',
                usernameVariable: 'DOCKER_USER',
                passwordVariable: 'DOCKER_PASS'
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
