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
                echo "Building backend image..."
                docker build -t backend-app backend
                '''
            }
        }

        stage('Deploy Backends') {
            steps {
                sh '''
                echo "Removing old containers..."
                docker rm -f backend1 backend2 || true
                docker network create lab-network || true

                echo "Starting backend1..."
                docker run -d --name backend1 --network lab-network backend-app \
                sh -c "echo Backend1 > index.html && python3 -m http.server 8080"

                echo "Starting backend2..."
                docker run -d --name backend2 --network lab-network backend-app \
                sh -c "echo Backend2 > index.html && python3 -m http.server 8080"

                echo "Waiting for backends..."
                sleep 8
                '''
            }
        }

        stage('Deploy NGINX') {
            steps {
                sh '''
                echo "Removing old NGINX..."
                docker rm -f nginx-lb || true

                echo "Starting NGINX..."
                docker run -d --name nginx-lb \
                --network lab-network \
                -p 80:80 nginx:latest

                echo "Waiting before copying config..."
                sleep 5

                echo "Copying config..."
                docker exec nginx-lb rm -f /etc/nginx/conf.d/default.conf || true
                docker cp nginx/default.conf nginx-lb:/etc/nginx/conf.d/default.conf

                echo "Testing config..."
                docker exec nginx-lb nginx -t

                echo "Reloading NGINX..."
                docker exec nginx-lb nginx -s reload
                '''
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully!"
        }
        failure {
            echo "Pipeline failed. Check logs."
        }
    }
}
