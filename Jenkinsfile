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
        sshagent(['k8s-server-key']) {
            sh '''
                scp -o StrictHostKeyChecking=no \
                    deployment.yaml \
                    service.yaml \
                    root@172.31.2.160:/root/

                ssh -o StrictHostKeyChecking=no \
                    root@172.31.2.160 "
                        sed -i 's/IMAGE_TAG/${BUILD_NUMBER}/g' /root/deployment.yaml

                        kubectl apply -f /root/deployment.yaml
                        kubectl apply -f /root/service.yaml

                        kubectl rollout status deployment/java-app
                    "
            '''
        }
    }
}
	
       
    }
}
