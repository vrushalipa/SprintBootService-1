pipeline {

 agent any

 stages {

  stage('Checkout') {
   steps {
    git branch: 'master',
    url: 'https://github.com/vrushalipa/SprintBootService-1.git'
   }
  }

  stage('Build') {
   steps {
    sh 'mvn clean package'
   }
  }

  stage('Docker Build') {
   steps {
    sh 'docker build -t springboot-app:v1 .'
   }
  }

  stage('Push ECR') {
   steps {
    sh '''
    aws ecr get-login-password \
    --region us-east-1 \
    | docker login \
    --username AWS \
    --password-stdin 621715857751.dkr.ecr.us-east-1.amazonaws.com

    docker tag springboot-app:v1 \
    621715857751.dkr.ecr.us-east-1.amazonaws.com/ecr-repository:v1

    docker push \
    621715857751.dkr.ecr.us-east-1.amazonaws.com/ecr-repository:v1
    '''
   }
  }

  stage('Deploy') {
   steps {
	 withCredentials([[
            $class: 'AmazonWebServicesCredentialsBinding',
            credentialsId: 'AWS-credentials'
        ]]) {
            sh '''
            export AWS_REGION=us-east-1
            export KUBECONFIG=/var/lib/jenkins/.kube/config

            aws sts get-caller-identity

            kubectl get nodes

            kubectl apply -f deployment.yaml

            kubectl apply -f service.yaml
            '''
   }
  }
 }
}
}
