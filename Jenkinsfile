def d = [
  'terraform.version':'1.0.0',
  'tfsec.version':'v0.57.1'
]

def props = [:]

podTemplate {
  node(POD_LABEL) {
    checkout scm
    props = readProperties(defaults: d, file: 'version.properties')
  }
}

pipeline {
  agent {
    kubernetes {
      yaml """
        apiVersion: v1
        kind: Pod
        spec:
          containers:
          - name: terraform
            image: hashicorp/terraform:${props["terraform.version"]}
            command:
            - cat
            tty: true
          - name: tfsec
            image: tfsec/tfsec:${props["tfsec.version"]}
            command:
            - cat
            tty: true
        """
    }
  }
  options {
    buildDiscarder(logRotator(numToKeepStr: '5'))
    durabilityHint('PERFORMANCE_OPTIMIZED')
    disableConcurrentBuilds()
  }
  stages{
    stage('init') {
      steps {
        container('terraform') {
          sh 'terraform init -no-color'
        }
      }
    }
    stage('plan') {
      steps {
        container('terraform') {
          sh 'terraform plan -out=tfplan -no-color '
        }
      }
    }
    stage('tfsec') {
      steps {
        container('tfsec') {
          sh '''
            tfsec -version
            tfsec . --no-color
          '''
        }
      }
    }
    stage('apply') {
      steps {
        container('terraform') {
          sh 'terraform apply -auto-approve -no-color tfplan'
        }
      }
    }
  }
}