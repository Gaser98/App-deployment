pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Build'
                script {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                        docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                            def imageName = "gaser98/app-jenk:v${BUILD_NUMBER}"
                            def dockerfile = "Dockerfile" // Path to your Dockerfile

                            // Build Docker image
                            docker.build(imageName, "-f ${dockerfile} .")

                            // Push Docker image
                            docker.image(imageName).push()
                        }
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
