pipeline {
  stages {

    stage('Build') {
      environment {
        DOCKERHUB_CREDS = credentials('dockerhub')
      }
      steps {
        container('docker') {
          // Build new image
          sh "until docker ps; do sleep 3; done && docker build -t spurthiaddagada/tomcat:${env.GIT_COMMIT} ."
          // Publish new image
          sh "docker login --username $DOCKERHUB_CREDS_USR --password $DOCKERHUB_CREDS_PSW && docker push spurthiaddagada/tomcat:${env.GIT_COMMIT}"
        }
      }
    }

    stage('Deploy E2E') {
      environment {
        GIT_CREDS = credentials('git')
      }
      steps {
        container('tools') {
          sh "git clone https://$GIT_CREDS_USR:$GIT_CREDS_PSW@github.com/manishjangid/swe645_project.git"
          sh "git config --global user.email 'spurthiaddagada@gmail.com'"

          dir("argocd-demo-deploy") {
            sh "cd ./prod && kustomize edit set image spurthiaddagada/tomcat:${env.GIT_COMMIT}"
            sh "git commit -am 'Publish new version' && git push || echo 'no changes'"
          }
        }
      }
    }
    
  }
}
