pipeline {
	agent any
	stages{
		stage('Checking out git repo') {
			steps{
			  echo 'Checkout...'
			  checkout scm
			}
		}
		stage("Linting") {
			steps {
			  echo 'Linting...'
			  sh '/bin/hadolint Dockerfile'
			  sh 'tidy -q -e *.html'
			}
		}

		stage('Building Docker image') {
			steps {
				echo 'Building Docker image ...'
				withCredentials([usernamePassword(credentialsId: 'Docker', passwordVariable: 'dockerPassword', usernameVariable: 'dockerUser')]) {
					sh "sudo docker login -u ${env.dockerUser} -p ${env.dockerPassword}"   
					sh "sudo docker build -t sunshine2050/cloud-devops-engineer-capstone ."
					sh "sudo docker push sunshine2050/cloud-devops-engineer-capstone"
				}
			}
		}
		stage('Deploying to Kubernetes locally') {
			steps{
				echo 'Deploying to Kubernetes ...'
				kubectl run microservice1 --image=sunshine2050/operationalize-a-machine-learning-microservice-api:latest --port=80
				
				kubectl get pods
				kubectl port-forward microservice1 8000:80
			}
		}
	}
}
