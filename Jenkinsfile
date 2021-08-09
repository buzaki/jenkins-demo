pipeline {
  agent {
    kubernetes {
      yamlFile "jenkins-pod.yaml"
    }
  }
  environment {
    PROJECT = "demo"
    REGISTRY_USER = "buzaki"
  }
  stages {
    stage("Build") {
      steps {
        container("kaniko") {
          sh "/kaniko/executor --context `pwd` --destination buzaki/jenkins-demo:latest --destination ${REGISTRY_USER}/${PROJECT}:${env.BRANCH_NAME.toLowerCase()}-${BUILD_NUMBER}"
        }
      }
    }
    stage("Deploy") {
      when { branch "master" }
      steps {
        container("kustomize") {
          sh """
            cd kustomize/overlays/production
            kustomize edit set image ${REGISTRY_USER}/${PROJECT}=${REGISTRY_USER}/${PROJECT}:$BRANCH_NAME-${BUILD_NUMBER}
            kustomize build . | kubectl apply --filename -
          """
        }
      }
    }
  }
}
