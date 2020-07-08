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
		stage('Deploying to AWS EKS') {
			steps{
				echo 'Deploying to AWS EKS...'
				dir ('./') {
					withAWS(credentials: 'Jenkins', region: 'us-east-2') {
						sh "aws eks --region us-east-2 update-kubeconfig --name sunshine2050/cloud-devops-engineer-capstone:latest"
					}
				}
			}
		}
	}
}
