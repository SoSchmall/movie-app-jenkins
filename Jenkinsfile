pipeline {
    environment {
        DOCKER_ID = "vikinghacker"
        DOCKER_IMAGE_MOVIE = "movie-app"
        DOCKER_IMAGE_CAST = "cast-app"
        // DOCKER_TAG = "v.${BUILD_ID}.0"
    }

agent any
    stages {
        
        stage('Build and start the services') {
            steps {
                script {
                sh '''
                docker-compose build
                docker-compose up -d
                '''
                }
            }
        }

        stage('Testing both movie and cast services') {
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

        stage('Build docker images') { 
            steps {
                script {
                sh '''
                docker build -t $DOCKER_ID/$DOCKER_IMAGE_MOVIE:$DOCKER_TAG .
                docker build -t $DOCKER_ID/$DOCKER_IMAGE_CAST:$DOCKER_TAG .
                sleep 6
                '''
                }
            }
        }

        stage('Stop the services from running') {
            steps {
                script {
                sh '''
                docker-compose down
                '''
                }
            }
        }

        stage('Push images built to dockerhub'){
            environment
            {
                DOCKER_PASS = credentials("DOCKER_HUB_PASS") 
            }
            steps {

                script {
                sh '''
                docker login -u $DOCKER_ID -p $DOCKER_PASS
                docker push $DOCKER_ID/$DOCKER_IMAGE_MOVIE:$DOCKER_TAG
                docker push $DOCKER_ID/$DOCKER_IMAGE_CAST:$DOCKER_TAG
            
                '''
                }
            }
        }

        stage('Development deployment') {
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

        stage('QA deployment') {
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

        stage('Staging deployment') {
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

        stage('Production deployment') {
            environment
            {
                KUBECONFIG = credentials("config")
                DOCKER_PASS = credentials("DOCKER_HUB_PASS")
            }
                steps {
                    timeout(time: 15, unit: "MINUTES") {
                        input message: 'Do you want to deploy in production ?', ok: 'Yes'
                    }
                    script {
                    sh '''
                    helm upgrade --install app-prod helm/ -f helm/values.prod.yaml --namespace prod --create-namespace
                    '''
                    }
                }
        }
    }

    // Send email if the job fails
    post {
        failure {
            echo "This will run if the job failed"
            mail to: "nawfal@gmail.com",
                subject: "${env.JOB_NAME} - Build # ${env.BUILD_ID} has failed",
                body: "For more info on the pipeline failure, check out the console output at ${env.BUILD_URL}"
        }
    
    }
}