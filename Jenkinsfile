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
    stage('Deploy in ECS') {
      steps {
        script {
          sh'''
ECR_IMAGE="130826749738.dkr.ecr.us-east-1.amazonaws.com/vote:${BUILD_NUMBER}"
TASK_DEFINITION=$(aws ecs describe-task-definition --task-definition "$TASK_FAMILY" --region "$AWS_DEFAULT_REGION")
NEW_TASK_DEFINTIION=$(echo $TASK_DEFINITION | jq --arg IMAGE "$ECR_IMAGE" '.taskDefinition | .containerDefinitions[0].image = $IMAGE | del(.taskDefinitionArn) | del(.revision) | del(.status) | del(.requiresAttributes) | del(.compatibilities) | del(.registeredAt) | del(.registeredBy)')
NEW_TASK_INFO=$(aws ecs register-task-definition --region "$AWS_DEFAULT_REGION" --cli-input-json "$NEW_TASK_DEFINTIION")
NEW_REVISION=$(echo $NEW_TASK_INFO | jq '.taskDefinition.revision')
aws ecs update-service --cluster ${ECS_CLUSTER} \
--service ${SERVICE_NAME} \
--task-definition ${TASK_FAMILY}:${NEW_REVISION}'''
        }

      }
    }
  }
}
