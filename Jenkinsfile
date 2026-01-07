pipeline {
  agent any

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build Images') {
      steps {
        sh 'docker compose build'
      }
    }

    stage('Deploy Application') {
      steps {
        sh 'docker compose up -d'
      }
    }

    stage('Health Check') {
      steps {
        sh 'curl -f http://localhost/app'
      }
    }
  }
}
