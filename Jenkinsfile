pipeline {
    agent any

    environment {
        // 🔧 IMAGE / REGISTRY
        // Option A: internal OpenShift registry (recommended for lab)
        REGISTRY          = 'image-registry.openshift-image-registry.svc:5000'
        OC_PROJECT        = 'bala12121986-dev'
        IMAGE_NAME        = 'enterprise-api'
        IMAGE_TAG         = "${env.BUILD_NUMBER}"

        // 🔧 OpenShift cluster
        OPENSHIFT_API     = 'https://api.rm2.thpm.p1.openshiftapps.com:6443'
        OPENSHIFT_PROJECT = 'bala12121986-dev'

        // 🔧 Helm chart
        HELM_RELEASE      = 'enterprise-api-helm'
        HELM_CHART_PATH   = 'helm/enterprise-api-helm'

        // 🔧 Simple flag: turn image build/push on/off
        ENABLE_BUILD_PUSH = 'false'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build & Push Image') {
            when {
                expression { env.ENABLE_BUILD_PUSH == 'true' }
            }
            steps {
                sh """
                  echo '*** Logging into OpenShift internal registry ***'
                  oc login ${OPENSHIFT_API} --token=$OC_TOKEN --insecure-skip-tls-verify=true
                  oc project ${OPENSHIFT_PROJECT}

                  # this logs docker into the internal registry
                  oc registry login

                  echo '*** Building image ***'
                  docker build -t ${REGISTRY}/${OC_PROJECT}/${IMAGE_NAME}:${IMAGE_TAG} .

                  echo '*** Pushing image ***'
                  docker push ${REGISTRY}/${OC_PROJECT}/${IMAGE_NAME}:${IMAGE_TAG}
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
                        --set image.repository=${REGISTRY}/${OC_PROJECT}/${IMAGE_NAME} \
                        --set image.tag=${IMAGE_TAG} \
                        -f ${HELM_CHART_PATH}/values.yaml
                    """
                }
            }
        }
    }
}
