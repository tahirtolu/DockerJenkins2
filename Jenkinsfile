pipeline {
    agent any

    environment {
        registryCredential = 'dockerhub-credentials' // Docker Hub için kullanıcı kimlik bilgileri
        dockerImage = ''
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    git branch: 'master', credentialsId: 'your-git-credentials', url: 'https://github.com/tahirtolu/DockerJenkins2.git'
                }
            }
        }

        stage('Build Maven') {
            tools {
                maven 'maven' // Jenkins konfigürasyonunda tanımladığınız Maven aracı adı
            }
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Stop and Remove Existing Container') {
            steps {
                script {
                    // Daha önce varsa mevcut konteyneri durdur ve kaldır
                    sh 'docker stop demo-container || true' // Hata almamak için || true ekleyin
                    sh 'docker rm demo-container || true' // Hata almamak için || true ekleyin
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("demo13:${env.BUILD_NUMBER}")
                }
            }
        }

        stage('Push Image to Hub') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', registryCredential) {
                        dockerImage.push("${env.BUILD_NUMBER}")
                        dockerImage.push("latest")
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
