pipeline {
    agent { label 'my-worker' }

    stages {
        stage('Build') {
            steps {
                echo 'build'
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
                echo 'deploy'
                script {
                    withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                        sh '''
                            export BUILD_NUMBER=$(cat ../build.txt)
                            mv Deployment/deployment.yml Deployment/deployment.yml.tmp
                            cat Deployment/deployment.yml.tmp | envsubst > Deployment/deployment.yml
                            rm -f Deployment/deployment.yml.tmp
                            kubectl apply -f Deployment --kubeconfig ${KUBECONFIG} -n ${BRANCH_NAME}
                        '''
                    }
                }
            }
        }
    }
}
