pipeline {
  agent { label 'linux'}
  options {
    skipDefaultCheckout(true)
  }
  environment {
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
    stage('terraform') {
      steps {
        sh './terraformw apply -auto-approve'
      }
    }
  }
  post {
    always {
      cleanWs()
    }
  }
}