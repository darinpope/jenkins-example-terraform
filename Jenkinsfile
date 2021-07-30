pipeline {
  agent { label 'linux'}
  options {
    skipDefaultCheckout(true)
  }
  environment {
    TF_VERSION = "1.0.0"
    PATH = "$WORKSPACE/.bin:$PATH"
  }
  stages{
    stage('clean workspace') {
      steps {
        cleanWs()
      }
    }
    stage('checkout') {
      steps {
        checkout scm
      }
    }
    stage('install terraform') {
      steps {
        sh './terraformw'
      }
    }
    stage('apply') {
      steps {
        sh 'terraform apply -auto-approve'
      }
    }
  }
  post {
    always {
      cleanWs()
    }
  }
}