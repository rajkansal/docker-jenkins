pipeline {
  agent any

  environment {
    DOCKERHUB_USER = "rajkansal"
    IMAGE_NAME = "backend-app"
  }

  stages {

    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build Docker Images') {
      steps {
        sh 'docker compose build'
      }
    }

    stage('Docker Login') {
      steps {
        withCredentials([usernamePassword(
          credentialsId: 'dockerhub',
          usernameVariable: 'USER',
          passwordVariable: 'PASS'
        )]) {
          sh 'echo $PASS | docker login -u $USER --password-stdin'
        }
      }
    }

    stage('Tag & Push Image') {
      steps {
        sh '''
        docker tag docker-compose1_backend $DOCKERHUB_USER/$IMAGE_NAME:latest
        docker push $DOCKERHUB_USER/$IMAGE_NAME:latest
        '''
      }
    }

    stage('Deploy Green') {
      steps {
        sh 'docker compose up -d backend-green'
      }
    }

    stage('Health Check Green') {
      steps {
        sh 'curl -f http://localhost:5001/health'
      }
    }

    stage('Switch Traffic') {
      steps {
        sh '''
        docker compose stop backend-blue || true
        docker compose up -d backend-green
        '''
      }
    }

    stage('Deploy to Kubernetes') {
      steps {
        sh '''
        kubectl apply -f backend-deployment.yaml
        kubectl rollout status deployment/backend
        '''
      }
    }
  }

  post {
    failure {
      echo "❌ Deployment failed — rolling back"
      sh 'kubectl rollout undo deployment/backend || true'
    }
  }
}
