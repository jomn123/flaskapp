pipeline {
    agent {
        docker {
            image 'docker:latest'
            args '-v /var/run/docker.sock:/var/run/docker.sock'
        }
    }
    
    stages {
        stage('Build') {
            steps {
                sh 'docker build -t myapp:${BUILD_NUMBER} .'
            }
        }
        
        stage('Test') {
            steps {
                sh 'docker run --rm myapp:${BUILD_NUMBER} python -m unittest discover tests'
            }
        }
        
        stage('Deploy') {
            steps {
                sh 'docker run -d -p 5000:5000 --name myapp_${BUILD_NUMBER} myapp:${BUILD_NUMBER}'
            }
        }
    }
    
    post {
        always {
            sh 'docker stop myapp_${BUILD_NUMBER} || true'
            sh 'docker rm myapp_${BUILD_NUMBER} || true'
        }
    }
}