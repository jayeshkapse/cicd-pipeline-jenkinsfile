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
                    checkout([$class: 'GitSCM', branches: [[name: 'main']], userRemoteConfigs: [[url: 'https://github.com/Shrirang-joshi17198/sample-app.git']]])
                }
            }
        }

        stage('Build and Test') {
            agent any
            steps {
                script {
                    docker.image('python:3.9-alpine').inside('-v $PWD:/app') {
                        sh 'pip install -r /app/requirements.txt'
                        sh 'pytest /app/app/tests/'
                    }
                }
            }
            post {
                always {
                    junit 'app/tests/junit_report.xml'
                }
            }
        }

        stage('Deploy') {
            agent any
            steps {
                script {
                    docker.image('python:3.9-alpine').inside('-v $PWD:/app') {
                        sh 'docker build -t my-python-app .'
                        sh 'docker run -p 5000:5000 my-python-app'
                    }
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
