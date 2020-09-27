// AWS Params
def ecrNameSpace = "navdeep-poc/hello"
def AWS_ACCOUNT_ID = "695292474035"
def AWS_REGION = "us-west-2"
def ecrUrl

// Pipeline
pipeline {
  agent any
  environment {
    DOCKER_CLI_EXPERIMENTAL=enabled
  }
  stages {
    stage('Checkout') {
      steps {
        script {
          ecrName        = ecrNameSpace
          ecrUrl         = AWS_ACCOUNT_ID + ".dkr.ecr." + AWS_REGION + ".amazonaws.com/" + ecrName
        }
      }
    }
    stage('Docker') {
      stages {
        stage('Build&Push') {
          steps {
            sh "make all"
            sh "aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${ecrUrl}"
            sh "docker tag ${ecrNameSpace}:amd64 ${ecrUrl}:amd64"
            sh "docker tag ${ecrNameSpace}:arm64 ${ecrUrl}:arm64"
            sh "docker push ${ecrUrl}:amd64"
            sh "docker push ${ecrUrl}:arm64"
            sh "docker manifest create ${ecrUrl} ${ecrUrl}:amd64 ${ecrUrl}:arm64"
            sh "docker manifest annotate --arch arm64 ${ecrUrl} ${ecrUrl}:arm64"
            sh "docker manifest push ${ecrUrl}"
          }
        }
      }
    }
  }
}
