pipeline {

    agent any

    stages {

        stage('Checkout') {
            steps {
                git branch: 'master',
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

        stage('Docker Build') {
            steps {
                sh 'docker build -t java-app:${BUILD_NUMBER} .'
            }
       }
	
	

	stage('Push to ECR') {
 	    steps {
       		 sh '''
           	 aws ecr get-login-password --region ap-south-1 | \
           	 docker login --username AWS --password-stdin \
            	621662900911.dkr.ecr.ap-south-1.amazonaws.com

            	docker tag java-app:${BUILD_NUMBER} \
            	621662900911.dkr.ecr.ap-south-1.amazonaws.com/java-app:${BUILD_NUMBER}

            	docker push \
            	621662900911.dkr.ecr.ap-south-1.amazonaws.com/java-app:${BUILD_NUMBER}
       		 '''
    		}
	}
		stage('Deploy to Kubernetes') {
    steps {
        sshagent(['kind-server']) {
            sh '''
                scp -o StrictHostKeyChecking=no \
                    deployment.yaml \
                    service.yaml \
                    ubuntu@172.31.2.160:/home/ubuntu/

                ssh -o StrictHostKeyChecking=no \
                    ubuntu@172.31.2.160 "
                        sed -i 's/IMAGE_TAG/${BUILD_NUMBER}/g' /home/ubuntu/deployment.yaml
                        kubectl apply -f /home/ubuntu/deployment.yaml
                        kubectl apply -f /home/ubuntu/service.yaml
                        kubectl rollout status deployment/java-app
                    "
            '''
        }
    }
}

	
       
    }
}
