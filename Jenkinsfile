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
                        sh '''
                                PATH=/home/jenkins/google-cloud-sdk/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
                                export BUILD_NUMBER=$(cat ../build.txt)
                                export check=$(helm list --short | grep "my-app-release")
                                if [ -z $check ]
                                then
                                    helm install my-app-release ./my-chart/  
                                else
                                    helm upgrade my-app-release ./my-chart/  
                                fi

                            '''
                    }
                }
            }
        }
    }
}
