pipeline {
  agent any

  stages {
    git branch : 'master'
    stage('Build') {
      steps {
        script {
          docker.withRegistry('https://gaser98/app-jenk', 'dockerhub') {
            def image = docker.build("gaser98/app-jenk:v${BUILD_NUMBER}")
            image.push()
          }
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
