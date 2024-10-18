pipeline {
    agent any
    
    environment {
        APP_NAME = "myapp"
        DOCKER_IMAGE = "${APP_NAME}:${BUILD_NUMBER}"
    }
    
    stages {
        stage('Build') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE} ."
            }
        }
        
        stage('Test') {
            steps {
                script {
                    def testResult = sh(script: "docker run --rm ${DOCKER_IMAGE} python -m unittest discover tests", returnStatus: true)
                    if (testResult != 0) {
                        error "Tests failed!"
                    }
                }
            }
        }
        
        stage('Deploy') {
            steps {
                sh """
                    docker stop ${APP_NAME} || true
                    docker rm ${APP_NAME} || true
                    docker run -d -p 5000:5000 --name ${APP_NAME} ${DOCKER_IMAGE}
                """
            }
        }
    }
    
    post {
        always {
            sh """
                docker stop ${APP_NAME} || true
                docker rm ${APP_NAME} || true
                docker rmi ${DOCKER_IMAGE} || true
            """
        }
    }
}