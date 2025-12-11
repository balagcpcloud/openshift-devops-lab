pipeline {
    agent any

    environment {
        // 🔧 TODO: update these for your setup
        REGISTRY          = 'quay.io/your-namespace'
        IMAGE_NAME        = 'enterprise-api'
        IMAGE_TAG         = "${env.BUILD_NUMBER}"

        OPENSHIFT_API     = 'https://api.rm2.thpm.p1.openshiftapps.com:6443'
        OPENSHIFT_PROJECT = 'bala12121986-dev'

        HELM_RELEASE      = 'enterprise-api-helm'
        HELM_CHART_PATH   = 'helm/enterprise-api-helm'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build & Push Image') {
            steps {
                sh """
                  echo '*** Building image ***'
                  docker build -t ${REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG} .
                  docker push ${REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG}
                """
            }
        }

        stage('Deploy to OpenShift via Helm') {
            steps {
                withCredentials([string(credentialsId: 'oc-token', variable: 'OC_TOKEN')]) {
                    sh """
                      echo '*** Login to OpenShift ***'
                      oc login ${OPENSHIFT_API} --token=$OC_TOKEN --insecure-skip-tls-verify=true
                      oc project ${OPENSHIFT_PROJECT}

                      echo '*** Helm upgrade/install ***'
                      helm upgrade --install ${HELM_RELEASE} ${HELM_CHART_PATH} \
                        --set image.repository=${REGISTRY}/${IMAGE_NAME} \
                        --set image.tag=${IMAGE_TAG}
                    """
                }
            }
        }
    }
}
