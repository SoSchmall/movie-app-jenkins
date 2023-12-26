pipeline {
    environment {
        DOCKER_ID = "vikinghacker"
        DOCKER_IMAGE_MOVIE = "movie-app"
        DOCKER_IMAGE_CAST = "cast-app"
        // DOCKER_TAG = "v.${BUILD_ID}.0"
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
        //         docker push $DOCKER_ID/$DOCKER_IMAGE_MOVIE:$DOCKER_TAG
                // docker push $DOCKER_ID/$DOCKER_IMAGE_CAST:$DOCKER_TAG
        //     
        //         '''
        //         }
        //     }
        // }

        stage('Deploiement en dev'){
            environment
            {
                KUBECONFIG = credentials("config")
                DOCKER_PASS = credentials("DOCKER_HUB_PASS")
            }
                steps {
                    script {
                    sh '''
                    helm upgrade --install app-dev helm/ -f helm/values.dev.yaml --namespace dev --create-namespace
                    '''
                    }
                }
        }


        stage('Deploiement en qa'){
            environment
            {
                KUBECONFIG = credentials("config")
                DOCKER_PASS = credentials("DOCKER_HUB_PASS")
            }
                steps {
                    script {
                    sh '''
                    helm upgrade --install app-qa helm/ -f helm/values.qa.yaml --namespace qa --create-namespace
                    '''
                    }
                }
        }


        stage('Deploiement en staging'){
            environment
            {
                KUBECONFIG = credentials("config")
                DOCKER_PASS = credentials("DOCKER_HUB_PASS")
            }
                steps {
                    script {
                    sh '''
                    helm upgrade --install app-staging helm/ -f helm/values.staging.yaml --namespace staging --create-namespace
                    '''
                    }
                }
        }





        stage('Deploiement en prod'){
                environment
                {
                KUBECONFIG = credentials("config")
                }
                    steps {
                        timeout(time: 15, unit: "MINUTES") {
                            input message: 'Do you want to deploy in production ?', ok: 'Yes'
                        }
                        script {
                        sh 
                        '''
                        helm upgrade --install app-prod helm/ -f helm/values.prod.yaml --namespace prod --create-namespace
                        '''
                        }
                    }
                }

        // stage('Prune Docker data') {
        //         steps {
        //             sh 'docker system prune -a --volumes -f'
        //         }

        // }
    }

    // post {

    //     failure {
    //         echo "This will run if the job failed"
    //         mail to: "nawfal.eddaoudi@gmail.com",
    //             subject: "${env.JOB_NAME} - Build # ${env.BUILD_ID} has failed",
    //             body: "For more info on the pipeline failure, check out the console output at ${env.BUILD_URL}"
    //     }
    
    // }
}