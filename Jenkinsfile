pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Build'
                script {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                        sh '''
                            docker login -u ${USERNAME} -p ${PASSWORD}
                            docker build -t gaser98/app-jenk:v${BUILD_NUMBER} .
                            docker push gaser98/app-jenk:v${BUILD_NUMBER}
                            echo ${BUILD_NUMBER} > ../build.txt
                        '''
                    }
                }
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
