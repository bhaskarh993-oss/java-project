pipeline {

    agent any

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/bhaskarh993-oss/java-project.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Package') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar',
                                  fingerprint: true
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying Java application...'
            }
        }
    }
}
