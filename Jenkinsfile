pipeline {
    agent any

    environment {
        IMAGE_NAME = "myapp"
        CONTAINER_NAME = "myapp-container"
        HOST_PORT = "8080"      // change this if already in use
        CONTAINER_PORT = "80"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${IMAGE_NAME}:latest ."
                }
            }
        }

        stage('Stop Old Container') {
            steps {
                script {
                    sh """
                        # Remove old container if it exists (running or stopped)
                        if [ \$(docker ps -aq -f name=${CONTAINER_NAME}) ]; then
                            echo "Removing old container..."
                            docker stop ${CONTAINER_NAME} || true
                            docker rm ${CONTAINER_NAME} || true
                        fi

                        # Free port if any other container is bound to HOST_PORT
                        RUNNING_CONTAINER=\$(docker ps -q --filter "publish=${HOST_PORT}")
                        if [ -n "\$RUNNING_CONTAINER" ]; then
                            echo "Port ${HOST_PORT} is in use by another container. Stopping it..."
                            docker stop \$RUNNING_CONTAINER || true
                            docker rm \$RUNNING_CONTAINER || true
                        fi
                    """
                }
            }
        }

        stage('Run New Container') {
            steps {
                script {
                    sh """
                        docker run -d \
                        --name ${CONTAINER_NAME} \
                        -p ${HOST_PORT}:${CONTAINER_PORT} \
                        ${IMAGE_NAME}:latest
                    """
                }
            }
        }
    }

    post {
        success {
            echo "🚀 App deployed successfully at http://<EC2-Public-IP>:${HOST_PORT}"
        }
        failure {
            echo "❌ Deployment failed. Check Jenkins logs."
        }
    }
}
