pipeline {
    agent {
        docker {
            image 'python:3.9-alpine'
            args '-v /var/run/docker.sock:/var/run/docker.sock'
        }
    }

    environment {
        HOME = '.'
    }

    stages {
        stage('Checkout') {
            steps {
                cleanWs()
                script {
                    checkout([$class: 'GitSCM', branches: [[name: 'main']], userRemoteConfigs: [[url: 'https://github.com/jayeshkapse/cicd-pipeline-jenkinsfile.git']]])
                }
            }
        }

        stage('Build and Test') {
            steps {
                script {
                    // Use the same Docker agent as defined globally
                    sh 'pwd'  // Print working directory for verification
                    sh 'pip install -r requirements.txt'
                    sh 'pytest app/tests/ --junitxml=app/tests/junit_report.xml'
                }
            }
            post {
                always {
                    junit 'app/tests/junit_report.xml'
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    sh 'docker build -t my-python-app .'
                    sh 'docker run -p 5000:5000 my-python-app'
                }
            }
            post {
                failure {
                    sh 'docker rm -f my-python-app'
                }
            }
        }
    }
}
