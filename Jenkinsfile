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
            sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 130826749738.dkr.ecr.us-east-1.amazonaws.com'
            sh 'sudo docker push 130826749738.dkr.ecr.us-east-1.amazonaws.com/vote:${BUILD_NUMBER}'
          }
        }

        stage('Unit Testing') {
          steps {
            sh 'echo Run the Test Cases'
          }
        }

      }
    }

    stage('Adding New Stage') {
      steps {
        sh 'echo adding a new stage'
      }
    }

  }
  environment {
    AWS_DEFAULT_REGION = 'us-east-1'
    SERVICE_NAME = 'vote'
    TASK_FAMILY = 'vote-fargate-v1'
    ECS_CLUSTER = 'vote-application'
  }
  post {
    always {
      deleteDir()
      sh 'sudo docker rmi 130826749738.dkr.ecr.us-east-1.amazonaws.com/vote:${BUILD_NUMBER}'
    }

  }
  options {
    buildDiscarder(logRotator(numToKeepStr: '10'))
    disableConcurrentBuilds()
    timeout(time: 1, unit: 'HOURS')
  }
}
