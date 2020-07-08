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
						sh "eksctl create cluster \
							--name capstone \
							--version 1.16 \
							--region us-east-2 \
							--nodegroup-name standard-workers \
							--node-type t2.micro \
							--nodes 1 \
							--nodes-min 1 \
							--nodes-max 1 \
							--managed"
					}
				}
			}
		}
	}
}
