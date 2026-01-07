pipeline {
  agent any

  environment {
    DOCKERHUB_USER = "DockerHub username"
  }

  stages {

    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build') {
      steps {
        sh 'docker compose build'
      }
    }

    stage('Login') {
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

    stage('Push') {
      steps {
        sh '''
        docker tag docker-compose1-backend $DOCKERHUB_USER/backend-app:latest
        docker push $DOCKERHUB_USER/backend-app:latest
        '''
      }
    }
  }
}
