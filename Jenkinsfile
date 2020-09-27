// AWS Params
def awsRegion = "us-west-2"
def ecrNameSpace = "navdeep-poc/hello"
def AWS_ACCOUNT_ID = "695292474035"
def AWS_REGION = "us-west-2"
def ecrUrl

// Pipeline
pipeline {
  agent any
  environment {
    MAVEN_OPTS='-Djava.awt.headless=true'
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
            sh "for i in amd64 arm64; do docker tag ${ecrNameSpace}:${i} ${ecrUrl}:${i} done"
            sh "for i in amd64 arm64; do docker push ${ecrUrl}:${i} done"
            sh "docker manifest create ${ecrUrl} ${ecrUrl}:amd64 ${ecrUrl}:arm64"
            sh "docker manifest annotate --arch arm64 ${ecrUrl} ${ecrUrl}:arm64"
            sh "docker manifest push ${ecrUrl}"
          }
        }
      }
    }
  }
}
