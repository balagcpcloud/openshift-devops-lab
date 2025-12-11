pipeline {
  agent any

  environment {
    // CHANGE these later to real values / Jenkins credentials
    OC_API_URL  = 'https://api.rm2.thpm.p1.openshiftapps.com:6443'
    OC_NAMESPACE = 'bala12121986-dev'
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Helm lint') {
      steps {
        sh '''
          echo "Running helm lint..."
          helm lint helm/enterprise-api-helm || echo "Helm not installed on Jenkins agent yet"
        '''
      }
    }

    stage('Deploy to OpenShift (placeholder)') {
      steps {
        sh '''
          echo "Here we will run:"
          echo "oc login $OC_API_URL --token=****"
          echo "oc project $OC_NAMESPACE"
          echo "helm upgrade --install enterprise-api-dev ./helm/enterprise-api-helm"
        '''
      }
    }
  }
}
