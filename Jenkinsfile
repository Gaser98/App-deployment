pipeline {
  agent any

  stages {
    stage('Git'){
            steps{
            echo'clone'
            git'https://github.com/Gaser98/App-deployment.git'
            }
        }
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
