pipeline {
  agent {
    label 'manual-slave'
  }
  stages {
    stage('Git Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build Docker Image') {
      parallel {
        stage('Build Docker Image') {
          steps {
            sh 'cd vote && sudo docker build . -t 130826749738.dkr.ecr.us-east-1.amazonaws.com/vote:${BUILD_NUMBER}'
            sh 'docker push 130826749738.dkr.ecr.us-east-1.amazonaws.com/vote:${BUILD_NUMBER}'
          }
        }
        stage('Unit Testing') {
          steps {
            sh 'echo Run the Test Cases'
          }
        }
      }
    }
  }
}
