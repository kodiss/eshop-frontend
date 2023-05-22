pipeline {
  agent {
    kubernetes {
      yaml """
      kind: Pod
      spec:
        containers:
        - name: kaniko
          image: gcr.io/kaniko-project/executor:v1.6.0-debug
          command: 
          - cat
          tty: true
          """
    }
  }
  environment {
    SCP_CREDS = credentials('scpCredentials')
    DOCKER_REGISTRY = "eshopregistry-dklbuzel.scr.kr-west.samsungsdscloud.com"
  }
  stages {
    stage('Build with Kaniko') {
      steps {
        script {
          container('kaniko') {
            dir('eshop-frontend') {
              def dockerAuth = sh(returnStdout: true, script: "echo -n \"${SCP_CREDS_USR}:${SCP_CREDS_PSW}\" | base64").trim().replaceAll(/\n/, '')            
              sh """
                rm -rf /kaniko/.docker
                mkdir /kaniko/.docker
                echo '{\"auths\":{\"${DOCKER_REGISTRY}\":{\"auth\":\"$dockerAuth\"}}}' > /kaniko/.docker/config.json
                cat /kaniko/.docker/config.json
                /kaniko/executor \
                  --git branch=main \
                  --context=. \
                  --destination=${DOCKER_REGISTRY}/eshop-frontend:latest
              """
            }
          }
        }
      }
    }
  }  
}