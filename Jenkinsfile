    pipeline {
        agent any

        environment {
            
            PATH = "/opt/google-cloud-sdk/bin:${env.PATH}"
            BASE_IMAGE_NAME = 'appflask'
            BASE_CONTAINER_NAME = 'appflask-container'
            DOCKER_CREDENTIALS_ID = 'dockerhub' // ID for Docker Hub password/token
            DOCKER_USERNAME_ID = 'dockerUsernameId' // ID for Docker Hub username
            GOOGLE_REGION = 'europe-west3' // Your Google Cloud region, removed trailing space
            GOOGLE_PROJECT_ID = 'testjenkinsdeployappflask' // Your Google Cloud project ID
            GOOGLE_CLOUD_SERVICE_ACCOUNT_KEY = 'google-cloud-service-account-key' // ID for Google Cloud service account key stored in Jenkins
        }

        stages {
            stage('Checkout') {
                steps {
                    git branch: 'main', url: 'https://github.com/YavorMarkov/appflask.git'
                }
            }

            stage('Build Docker Image') {
                steps {
                    script {
                        def dockerUsername = '' // Variable to store the Docker username
                        def random = new Random()
                        def randomNumber = random.nextInt(900000) + 100000
                        // Retrieve Docker username from credentials
                        withCredentials([string(credentialsId: "${env.DOCKER_USERNAME_ID}", variable: 'USERNAME')]) {
                            dockerUsername = USERNAME.trim()
                        }
                        def imageName = "${dockerUsername}/${env.BASE_IMAGE_NAME}-${randomNumber}:${env.BUILD_ID}"
                        // Build Docker image
                        docker.build(imageName)
                        env.IMAGE_NAME = imageName
                    }
                }
            }

            stage('Run Container') {
                steps {
                    script {
                        def containerName = "${env.BASE_CONTAINER_NAME}-${env.BUILD_ID}"
                        sh "docker run -d --name ${containerName} -p 8080:8080 ${env.IMAGE_NAME}"
                        env.CONTAINER_NAME = containerName
                    }
                }
            }

            // stage('Test') {
            //     steps {
            //         script {
            //             sh "docker exec ${env.CONTAINER_NAME} python -m pytest"
            //             sh "echo 'true' > tests_passed"
            //         }
            //     }
            // }

            stage('Push Image') {
                // // when {
                // //     expression {
                // //         readFile('tests_passed').trim() == 'true'
                // //     }
                // }
                steps {
                    script {
                        // Logging in to Docker Hub and pushing the image using stored Docker Hub credentials
                        docker.withRegistry('https://registry.hub.docker.com', env.DOCKER_CREDENTIALS_ID) {
                            docker.image(env.IMAGE_NAME).push()
                        }
                    }
                }
            }


            // stage('Enable Google Cloud API') {
            //     steps {
            //         script {
            //             // Attempt to enable the API, handling potential errors or delays
            //             def enableApiCmd = '/opt/google-cloud-sdk/bin/gcloud services enable run.googleapis.com --project=testjenkinsdeployappflask'
            //             try {
            //                 sh(enableApiCmd)
            //             } catch (Exception e) {
            //                 echo "Failed to enable API immediately, retrying..."
            //                 sleep(60) // Wait 60 seconds before retrying
            //                 sh(enableApiCmd)
            //             }
            //         }
            //     }
            // }

                stage('Deploy to Google Cloud Run') {
            //     when {
            //         expression {
            //             readFile('tests_passed').trim() == 'true'
            //         }
            //     }
                steps {
                    script {
                        // Securely handling Google Cloud credentials
                        withCredentials([file(credentialsId: env.GOOGLE_CLOUD_SERVICE_ACCOUNT_KEY, variable: 'GOOGLE_APPLICATION_CREDENTIALS')]) {
                            // Authenticate with Google Cloud using the service account key file
                            sh '/opt/google-cloud-sdk/bin/gcloud auth activate-service-account --key-file="$GOOGLE_APPLICATION_CREDENTIALS"'

                            // Configure Docker to use the gcloud command-line tool as a credential helper
                            sh '/opt/google-cloud-sdk/bin/gcloud auth configure-docker'
                            
                            // Define Google Cloud Run service name
                            def serviceName = "${env.BASE_IMAGE_NAME}-service"
                            
                            // Deploy to Google Cloud Run with minimum resources for cost-optimization
                            sh """
                            /opt/google-cloud-sdk/bin/gcloud run deploy ${serviceName} \\
                                --image=${env.IMAGE_NAME} \\
                                --platform=managed \\
                                --region=${env.GOOGLE_REGION} \\
                                --allow-unauthenticated \\
                                --project=${env.GOOGLE_PROJECT_ID} \\
                                --memory=256Mi \\
                                --cpu=1 \\
                                --max-instances=1 \\
                                --service-account=568066361996-compute@developer.gserviceaccount.com
                            """
                        }
                    }
                }   
            }
        }

        // Uncomment and adjust the post section if needed for cleanup actions
        /*
        post {
            always {
                script {
                    // Cleanup: Remove container and image, and delete tests_passed file
                    sh "docker rm -f ${env.CONTAINER_NAME} || true"
                    sh "docker rmi ${env.IMAGE_NAME} || true"
                    sh "rm -f tests_passed"
                }
            }
        }
        */
    }
