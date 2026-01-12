pipeline {
    agent any
    
    tools {
        nodejs "Node"
    }
    
    environment {
        FRONTEND_IMAGE_NAME = "chat-app-frontend"
        BACKEND_IMAGE_NAME = "chat-app-backend"
        FRONTEND_DOCKER_IMAGE = "pranay801/${FRONTEND_IMAGE_NAME}"
        BACKEND_DOCKER_IMAGE = "pranay801/${BACKEND_IMAGE_NAME}"
        IMAGE_TAG = "${BUILD_NUMBER}"
    }
    
    stages {
        stage ("code checkout") {
            steps {
                git branch: "main", url: "https://github.com/Pranay0189/chat-application.git"
            }
        }
        
        stage ("install-dependencies") {
            steps {
                sh '''
                    cd frontend
                    npm install
                '''
            }
        }
        
        stage ("docker login") {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'docker-credentials',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    '''
                }
            }
        }
        
        stage ("docker image build frontend") {
            steps {
                sh '''
                    cd frontend 
                    docker build --no-cache -t ${FRONTEND_DOCKER_IMAGE}:${IMAGE_TAG} .
                '''
            }
        }
        
        stage ("frontend image scan") {
            steps {
                sh '''
                    trivy image --severity HIGH,CRITICAL --exit-code 1 ${FRONTEND_DOCKER_IMAGE}:${IMAGE_TAG}
                '''
            }
        }
        
        stage ("docker image build backend") {
            steps {
                sh '''
                    cd backend
                    docker build --no-cache -t ${BACKEND_DOCKER_IMAGE}:${IMAGE_TAG} .
                '''
            }
        }
        
        stage ("docker image push") {
            steps {
                sh '''
                    docker push ${FRONTEND_DOCKER_IMAGE}:${IMAGE_TAG}
                    
                    echo "Successfully image pushed to docker hub"
                    
                    sleep 5
                    
                    docker push ${BACKEND_DOCKER_IMAGE}:${IMAGE_TAG}
                    
                    echo "Successfully image pushed to docker hub"
                '''
            }
        }
    }
}