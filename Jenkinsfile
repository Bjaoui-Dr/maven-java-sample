pipeline {
    agent any
    tools {maven 'M3'}
    stages {
        // stage('Git checkout') {
        //     steps {
        //         echo 'Git checkout'
        //         git branch: 'main', url: 'https://github.com/Bjaoui-Dr/maven-java-sample.git'
        //     }
        // }
        // stage('Check Workspace') {
        //     steps {
        //         sh 'ls -R target/surefire-reports'
        //     }
        // }
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
        stage('Deploy') {
            steps {
                sh 'java -jar -DServer.port=8001 target/spring-petclinic-2.3.1.BUILD-SNAPSHOT.jar'  
            }
        }
    }
}
