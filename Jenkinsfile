pipeline {
    environment {
    DOCKER_ID = "vikinghacker"
    DOCKER_IMAGE_MOVIE = "movie-app"
    DOCKER_IMAGE_CAST = "cast-app"
    DOCKER_TAG = "v.${BUILD_ID}.0"
}

agent any
stages {
        // stage(' Docker Build') { 
        //     steps {
        //         script {
        //         sh '''
        //         docker build -t $DOCKER_ID/$DOCKER_IMAGE_MOVIE:$DOCKER_TAG .
                //    docker build -t $DOCKER_ID/$DOCKER_IMAGE_CAST:$DOCKER_TAG .
        //         sleep 6
        //         '''
        //         }
        //     }
        // }
        // stage(' Docker run') {
        //         steps {
        //             script {
        //             sh '''
        //             echo "Cleaning existing container if exist"
        //             docker run -d -p 80:5000 --name fastapi $DOCKER_ID/$DOCKER_IMAGE:$DOCKER_TAG
        //             sleep 10
        //             '''
        //             }
        //         }
        //     }

        // stage('Test Acceptance'){
        //     steps {
        //             script {
        //             sh '''
        //             curl -X GET -i http://0.0.0.0:80
        //             '''
        //             }
        //     }

        // }

        // stage('Docker Push'){
        //     environment
        //     {
        //         DOCKER_PASS = credentials("DOCKER_HUB_PASS") 
        //     }
        //     steps {

        //         script {
        //         sh '''
        //         docker login -u $DOCKER_ID -p $DOCKER_PASS
        //         docker push $DOCKER_ID/$DOCKER_IMAGE:$DOCKER_TAG
        //         '''
        //         }
        //     }
        // }

        stage('Deploiement en dev'){
            environment
            {
                KUBECONFIG = credentials("kube-config")
                DOCKER_PASS = credentials("DOCKER_HUB_PASS")
            }
                steps {
                    script {
                    sh '''
                    helm upgrade --install app-dev templates/ -f values.dev.yaml --namespace dev --create-namespace
                    '''
                    }
                }
        }


        stage('Deploiement en qa'){
            environment
            {
                KUBECONFIG = credentials("kube-config")
                DOCKER_PASS = credentials("DOCKER_HUB_PASS")
            }
                steps {
                    script {
                    sh '''
                    helm upgrade --install app-qa templates/ -f values.dev.yaml --namespace dev --create-namespace
                    '''
                    }
                }
        }


        stage('Deploiement en staging'){
            environment
            {
                KUBECONFIG = credentials("kube-config")
                DOCKER_PASS = credentials("DOCKER_HUB_PASS")
            }
                steps {
                    script {
                    sh '''
                    helm upgrade --install app-staging templates/ -f values.dev.yaml --namespace dev --create-namespace
                    '''
                    }
                }
        }





        stage('Deploiement en prod'){
                environment
                {
                KUBECONFIG = credentials("EKS-config")
                }
                    steps {
                        timeout(time: 15, unit: "MINUTES") {
                            input message: 'Do you want to deploy in production ?', ok: 'Yes'
                        }
                        script {
                        sh 
                        '''
                        helm upgrade --install app-prod templates/ -f values.dev.yaml --namespace dev --create-namespace
                        '''
                        }
                    }
                }

        stage('Prune Docker data') {
                steps {
                    sh 'docker system prune -a --volumes -f'
                }

        }
    }
        // post { // send email when the job has failed
        //     success {
        //         script {
        //             slackSend botUser: true, color: 'good', message: 'Successful :jenkins-${JOB_NAME}-${BUILD_ID}', teamDomain: 'DEVOPS TEAM', tokenCredentialId: 'slack-bot-token'
        //         }
        //     }
            
        //     failure {
        //         script {
        //             slackSend botUser: true, color: 'danger', message: 'Failure :jenkins-${JOB_NAME}-${BUILD_ID}', teamDomain: 'DEVOPS TEAM', tokenCredentialId: 'slack-bot-token'
        //         }
        //     }
            // ..
            /*
            failure {
                echo "This will run if the job failed"
                mail to: "youssef.kadi@gmail.com",
                    subject: "${env.JOB_NAME} - Build # ${env.BUILD_ID} has failed",
                    body: "For more info on the pipeline failure, check out the console output at ${env.BUILD_URL}"
            }
            */
            // ..
       // }
    }