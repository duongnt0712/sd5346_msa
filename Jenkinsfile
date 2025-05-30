// Jenkins CI Pipeline - Build & Push Docker Image to ECR
pipeline {
  agent any

  environment {
    AWS_REGION        = 'ap-southeast-1'
    AWS_ACCOUNT_ID    = '975050299554'
    ECR_REPO          = 'practical-devops-ecr'
    EKS_CLUSTER_NAME  = 'my-eks-cluster'

    FRONTEND_TAG      = "frontend-${BUILD_NUMBER}"
    BACKEND_TAG       = "backend-${BUILD_NUMBER}"

    FRONTEND_IMAGE    = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO}"
    BACKEND_IMAGE     = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO}"
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Login to ECR') {
      steps {
        withCredentials([usernamePassword(
          credentialsId: 'aws-credentials', 
          usernameVariable: 'AWS_ACCESS_KEY_ID',
          passwordVariable: 'AWS_SECRET_ACCESS_KEY'
        )]) {
          sh '''
            aws configure set aws_access_key_id $AWS_ACCESS_KEY_ID
            aws configure set aws_secret_access_key $AWS_SECRET_ACCESS_KEY
            aws ecr get-login-password --region $AWS_REGION | \
              docker login --username AWS --password-stdin $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com
          '''
        }
      }
    }

    stage('Build Docker Images') {
      steps {
        sh '''
          docker build -t $FRONTEND_IMAGE:$FRONTEND_TAG ./src/frontend
          docker build -t $BACKEND_IMAGE:$BACKEND_TAG ./src/backend
        '''
      }
    }

    stage('Push Docker Images to ECR') {
      steps {
        sh '''
          docker push $FRONTEND_IMAGE:$FRONTEND_TAG
          docker push $BACKEND_IMAGE:$BACKEND_TAG
        '''
      }
    }
  }

  post {
    success {
      echo 'Pipeline Succeeded!'
    }
    failure {
      echo 'Pipeline Failed!'
    }
  }
}