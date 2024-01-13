pipeline {
    agent {
        docker {
            image 'python:3.9-alpine'
            args '-v /var/run/docker.sock:/var/run/docker.sock'  // For Docker-in-Docker builds
        }
    }

    stages {
        stage('Build') {
            steps {
                sh 'pip install -r requirements.txt'
            }
        }

        stage('Test') {
            steps {
                sh 'pytest app/tests/'
            }
            post {
                always {
                    junit 'app/tests/junit_report.xml'  // Assuming JUnit-style test reports
                }
            }
        }

        stage('Deploy') {
            steps {
                // Replace with your deployment steps, e.g., using Docker plugins
                sh 'docker build -t my-python-app .'
                sh 'docker run -p 5000:5000 my-python-app'
            }
            post {
                failure {
                    sh 'docker rm -f my-python-app'  // Rollback logic for Docker containers
                }
            }
        }
    }
}
