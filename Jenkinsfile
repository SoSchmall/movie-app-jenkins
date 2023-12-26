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


        stage('Docker up'){
            steps {
                script {
                sh '''
                docker-compose build
                docker-compose up -d
                '''
                }
            }
        }

        stage('Image test') {
            steps {
                script {
                    def movieServiceStatus = sh(script: 'curl -s -o /dev/null -w "%{http_code}" http://0.0.0.0:8001/api/v1/movies/docs', returnStdout: true).trim()
                    def castServiceStatus = sh(script: 'curl -s -o /dev/null -w "%{http_code}" http://0.0.0.0:8002/api/v1/casts/docs', returnStdout: true).trim()
        
                    echo "Display movieServiceStatus: ${movieServiceStatus}"
                    echo "Display castServiceStatus: ${castServiceStatus}"

                    if (movieServiceStatus == '200' && castServiceStatus == '200') {
                        echo "Movie service and cast service are running fine"
                    } else {
                        error("One or more services are not working as expected, please check the pipeline logs")
                    }
                }                           
            }
        }   
       


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

        // stage('Deploiement en dev') {
        //     environment
        //     {
        //         KUBECONFIG = credentials("config")
        //         DOCKER_PASS = credentials("DOCKER_HUB_PASS")
        //     }
        //         steps {
        //             script {
        //             sh '''
        //             helm upgrade --install app-dev helm/ -f helm/values.dev.yaml --namespace dev --create-namespace
        //             '''
        //             }
        //         }
        // }

        // stage('Deploiement en qa') {
        //     environment
        //     {
        //         KUBECONFIG = credentials("config")
        //         DOCKER_PASS = credentials("DOCKER_HUB_PASS")
        //     }
        //         steps {
        //             script {
        //             sh '''
        //             helm upgrade --install app-qa helm/ -f helm/values.qa.yaml --namespace qa --create-namespace
        //             '''
        //             }
        //         }
        // }

        // stage('Deploiement en staging') {
        //     environment
        //     {
        //         KUBECONFIG = credentials("config")
        //         DOCKER_PASS = credentials("DOCKER_HUB_PASS")
        //     }
        //         steps {
        //             script {
        //             sh '''
        //             helm upgrade --install app-staging helm/ -f helm/values.staging.yaml --namespace staging --create-namespace
        //             '''
        //             }
        //         }
        // }

        // stage('Deploiement en prod') {
        //     environment
        //     {
        //         KUBECONFIG = credentials("config")
        //         DOCKER_PASS = credentials("DOCKER_HUB_PASS")
        //     }
        //         steps {
        //             timeout(time: 15, unit: "MINUTES") {
        //                 input message: 'Do you want to deploy in production ?', ok: 'Yes'
        //             }
        //             script {
        //             sh '''
        //             helm upgrade --install app-prod helm/ -f helm/values.prod.yaml --namespace prod --create-namespace
        //             '''
        //             }
        //         }
        // }





        

        // stage('Prune Docker data') {
        //         steps {
        //             sh 'docker system prune -a --volumes -f'
        //         }

        // }
    }

    // post {

    //     failure {
    //         echo "This will run if the job failed"
    //         mail to: "nawfal@gmail.com",
    //             subject: "${env.JOB_NAME} - Build # ${env.BUILD_ID} has failed",
    //             body: "For more info on the pipeline failure, check out the console output at ${env.BUILD_URL}"
    //     }
    
    // }
}