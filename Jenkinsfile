pipeline {
    agent any
    
    stages {
        stage('Build') {
            steps {
                script {
                    docker.build("myapp:${BUILD_NUMBER}")
                }
            }
        }
        
        stage('Test') {
            steps {
                script {
                    docker.image("myapp:${BUILD_NUMBER}").inside {
                        sh 'python -m unittest discover tests'
                    }
                }
            }
        }
        
        stage('Deploy') {
            steps {
                script {
                    docker.image("myapp:${BUILD_NUMBER}").withRun('-p 5000:5000') { c ->
                        // The container is now running. You can perform additional steps here if needed.
                        sh 'echo "Application deployed on port 5000"'
                    }
                }
            }
        }
    }
    
    post {
        always {
            script {
                sh "docker rmi myapp:${BUILD_NUMBER} || true"
            }
        }
    }
}