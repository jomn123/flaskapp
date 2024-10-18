pipeline {
    agent any
    
    environment {
        APP_NAME = "myapp"
        DOCKER_IMAGE = "${APP_NAME}:${BUILD_NUMBER}"
        PREV_BUILD_NUMBER = "${BUILD_NUMBER.toInteger() - 1}"
    }
    
    stages {
        stage('Prepare') {
            steps {
                script {
                    // Stop and remove the old container if it exists
                    sh "docker stop ${APP_NAME} || true"
                    sh "docker rm ${APP_NAME} || true"
                }
            }
        }

        stage('Build') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE} ."
            }
        }
        
        stage('Test') {
            steps {
                script {
                    def testResult = sh(script: "docker run --rm ${DOCKER_IMAGE} sh -c 'cd /app && python -m unittest discover -v tests'", returnStatus: true)
                    if (testResult != 0) {
                        error "Tests failed!"
                    }
                }
            }
        }
        
        stage('Deploy') {
            steps {
                sh "docker run -d -p 5000:5000 --name ${APP_NAME} ${DOCKER_IMAGE}"
            }
        }

        stage('Cleanup') {
            steps {
                script {
                    // Remove the previous image if it exists
                    sh "docker rmi ${APP_NAME}:${PREV_BUILD_NUMBER} || true"
                }
            }
        }
    }
    
    post {
        failure {
            echo "Pipeline failed. The old container and image (if they exist) have been kept."
        }
        success {
            echo "Pipeline succeeded. The new container is running and the old image has been removed."
        }
    }
}