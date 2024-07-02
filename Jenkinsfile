pipeline {
    agent any

    tools {
        maven 'maven'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scmGit(
                    branches: [[name: '*/master']],
                    userRemoteConfigs: [[url: 'https://github.com/tahirtolu/DockerJenkins2']]
                )
            }
        }

        stage('Build Maven') {
            steps {
                bat 'mvn clean install'
            }
        }

        stage('Stop and Remove Existing Container') {
            steps {
                script {
                    bat 'docker stop welcome-to-container'
                    bat 'docker rm welcome-to-container'
                }
            }
        }

        stage('Build Docker Image') {
            when {
                not {
                    expression {
                        currentBuild.result == null || currentBuild.result == 'FAILURE'
                    }
                }
            }
            steps {
                script {
                    docker.build("demo13:${env.BUILD_NUMBER}")
                }
            }
        }

        stage('Push Image to Hub') {
            when {
                not {
                    expression {
                        currentBuild.result == null || currentBuild.result == 'FAILURE'
                    }
                }
            }
            steps {
                script {
                    docker.image("demo13:${env.BUILD_NUMBER}").run("-d -p 8080:8080 --name welcome-to-containerr")
                }
            }
        }
    }
}
