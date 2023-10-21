pipeline {
    agent any
    tools {maven 'M3'}
    environment {
        dockerHubUsername = 'drissbjaoui'
        imageName = "${dockerHubUsername}/spring-petclinic"
        
    }
    stages {
        stage('Build') {
            steps {
                sh 'mvn compile'  
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Containerize') {
            steps {
                script {
                    sh "mvn spring-boot:build-image -Dspring-boot.build-image.imageName=${imageName}:${BUILD_NUMBER}"
                }
            }
        }
        stage('Push to Docker Hub') {
    steps {
        script {
            def dockerImageName = "${imageName}:${BUILD_NUMBER}"

            // Login to Docker Hub using Jenkins credentials
            docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
                // Push the Docker image to Docker Hub
                docker.image(dockerImageName).push()
            }
        }
    }
}
stage('Deploy to Azure VM') {
            steps {
                script {
                    def azureVmHost = '40.67.225.179'
                    def azureVmUser = 'adminuser'
                    def dockerImageName = "${imageName}:${BUILD_NUMBER}"

                    sshagent(['azure-vm']) {
                        sh "sed -i \"s|{IMAGE}|${dockerImageName}|g\" docker-compose.yml"

                        sh "scp -o StrictHostKeyChecking=no docker-compose.yml ${azureVmUser}@${azureVmHost}:/home/adminuser"
                        
                        sh "ssh -o StrictHostKeyChecking=no ${azureVmUser}@${azureVmHost} 'sudo docker compose up -d'"
                    }
                }
            }
        }

        stage('Package') {
            steps {
                sh 'mvn package'
            }
            
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
                success {
                     archiveArtifacts artifacts: 'target/*.jar', followSymlinks: false
                }
            }
    }
}
}
