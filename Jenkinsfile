def d = [
  'terraform.version':'1.0.0',
  'tfsec.version':'v0.57.1'
]

def props = [:]

node {
  stage('Read parameters from version.properties'){
    checkout scm
    props = readProperties(defaults: d, file: 'version.properties')
  }
}

pipeline {
  agent {
    kubernetes {
      yaml '''
        apiVersion: v1
        kind: Pod
        spec:
          containers:
          - name: terraform
            image: terraform:${props["terraform.version"]}
            command:
            - cat
            tty: true
          - name: tfsec
            image: tfsec/tfsec-ci:${props["tfsec.version"]}
            command:
            - cat
            tty: true
        '''
    }
  }
  stages{
    stage('plan') {
      steps {
        container('terraform') {
          sh 'terraform plan -no-color'
        }
      }
    }
    stage('tfsec') {
      steps {
        container('tfsec') {
          sh '''
            tfsec . --no-color
          '''
        }
      }
    }
    stage('apply') {
      steps {
        container('terraform') {
          sh 'terraform apply -auto-approve -no-color'
        }
      }
    }
  }
}