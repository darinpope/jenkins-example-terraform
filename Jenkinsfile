def d = [
  'terraform.version':'1.0.0',
  'terrascan.version':'1.10.0'
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
          securityContext:
            runAsUser: 1000
          containers:
          - name: terraform
            image: hashicorp/terraform:${props["terraform.version"]}
            command:
            - cat
            tty: true
          - name: terrascan
            image: accurics/terrascan:${props["terrascan.version"]}
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
          sh 'terraform plan -out=tfplan -no-color'
        }
      }
    }
    stage('terrascan') {
      steps {
        container('terrascan') {
          sh '''
            terrascan init --config-path `pwd`
            terrascan scan --config-path `pwd`
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