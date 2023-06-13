pipeline {
  agent any

  stages {
    stage('Build') {
      steps {
        script {
          docker.build('gaser98/app-jenk:v${BUILD_NUMBER}').push()
        }
      }
    }

    stage('Deploy') {
      environment {
        KUBECONFIG = credentials('kubeconfig')
      }
      steps {
        withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
          sh '''
            kubectl --kubeconfig=${KUBECONFIG} apply -f Deployment/deploy.yml -n release
          '''
        }
      }
    }
  }
}
