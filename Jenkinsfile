pipeline {
    agent any

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Backend Image') {
            steps {
                sh '''
                echo "Building backend Docker image..."
                docker build -t backend-app backend
                '''
            }
        }

        stage('Deploy Backends') {
            steps {
                sh '''
                echo "Removing old backend containers..."
                docker rm -f backend1 backend2 || true

                echo "Creating Docker network..."
                docker network create lab-network || true

                echo "Starting backend containers..."
                docker run -d --name backend1 --network lab-network backend-app
                docker run -d --name backend2 --network lab-network backend-app

                echo "Waiting for backends to initialize..."
                sleep 10
                '''
            }
        }

        stage('Deploy NGINX') {
            steps {
                sh '''
                echo "Removing old NGINX container..."
                docker rm -f nginx-lb || true

                echo "Starting NGINX container..."
                docker run -d --name nginx-lb \
                --network lab-network \
                -p 80:80 nginx:latest

                echo "Waiting before configuring NGINX..."
                sleep 10

                echo "Copying NGINX config..."
                docker exec nginx-lb rm -f /etc/nginx/conf.d/default.conf || true
                docker cp nginx/default.conf nginx-lb:/etc/nginx/conf.d/default.conf

                echo "Testing NGINX config..."
                docker exec nginx-lb nginx -t

                echo "Reloading NGINX..."
                docker exec nginx-lb nginx -s reload
                '''
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully."
        }
        failure {
            echo "Pipeline failed. Check console logs."
        }
    }
}
