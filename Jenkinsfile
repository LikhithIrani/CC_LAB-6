pipeline {
    agent any

    stages {
        stage('Clone') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t backend-app ./backend'
            }
        }

        stage('Run Container') {
            steps {
                sh 'docker run -d --name backend-container backend-app'
            }
        }
    }
}
