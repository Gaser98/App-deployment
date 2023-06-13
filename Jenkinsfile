pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Build'
                script {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                        docker.withRegistry('https://registry.example.com', 'dockerhub') {
                            def image = docker.build("gaser98/app-jenk:v${BUILD_NUMBER}")
                            image.push()
                        }
                        sh "echo ${BUILD_NUMBER} > ../build.txt"
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
