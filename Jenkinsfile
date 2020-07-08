node {
    stage('Checking out git repo') {
      echo 'Checkout...'
      checkout scm
    }
    stage("Linting") {
      echo 'Linting...'
      sh '/bin/hdolint Dockerfile'
    }

    stage('Building Docker image') {
	    echo 'Building Docker image ...'
      withCredentials([usernamePassword(credentialsId: 'Docker', passwordVariable: 'dockerPassword', usernameVariable: 'dockerUser')]) {
	 sh "sudo docker login -u ${env.dockerUser} -p ${env.dockerPassword}"   
         sh "sudo docker build -t sunshine2050/app"
	}
    }
    stage('Deploying to AWS EKS') {
      echo 'Deploying to AWS EKS...'
      dir ('./') {
        withAWS(credentials: 'Jenkins', region: 'us-east-2') {
            sh "aws eks --region us-east-2 update-kubeconfig --name app"
            sh "kubectl get nodes"
            sh "kubectl get pods"
        }
      }
    }
}
