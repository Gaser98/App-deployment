pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Build'
            }
        }
    
        stage('Deploy') {
            steps {
                echo 'Deploy'
                script {
                    withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                        sh '''
                            export BUILD_NUMBER=$(cat ../build.txt)
                            mv Deployment/deploy.yml Deployment/deploy.yml.tmp
                            cat Deployment/deploy.yml.tmp | envsubst > Deployment/deploy.yml
                            rm -f Deployment/deploy.yml.tmp
                            kubectl apply -f Deployment --kubeconfig ${KUBECONFIG} -n release
                        '''
                    }
                }
            }
        }
    }
}
