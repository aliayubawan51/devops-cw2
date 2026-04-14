pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = "akhan360/cw2-server"
        DOCKER_TAG = "latest"
        PRODUCTION_SERVER = "98.86.224.92"
    }
    
    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out code from GitHub...'
                checkout scm
            }
        }
        
        stage('Build Docker Image') {
            steps {
                echo 'Building Docker Image...'
                sh 'docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} .'
                sh 'docker build -t ${DOCKER_IMAGE}:${BUILD_NUMBER} .'
            }
        }
        
        stage('Test Container') {
            steps {
                echo 'Testing Docker Container...'
                sh 'docker run -d --name test-container -p 8082:8081 ${DOCKER_IMAGE}:${DOCKER_TAG}'
                sh 'sleep 5'
sh 'docker exec test-container node --version'
                sh 'docker stop test-container'
                sh 'docker rm test-container'
            }
        }
        
        stage('Push to DockerHub') {
            steps {
                echo 'Pushing to DockerHub...'
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh 'docker push ${DOCKER_IMAGE}:${DOCKER_TAG}'
                    sh 'docker push ${DOCKER_IMAGE}:${BUILD_NUMBER}'
                }
            }
        }
        
        stage('Deploy to Kubernetes') {
            steps {
                echo 'Deploying to Kubernetes...'
                sshagent(['production-server']) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no ubuntu@${PRODUCTION_SERVER} \
                        "kubectl set image deployment/cw2-server cw2-server=${DOCKER_IMAGE}:${DOCKER_TAG} && \
                        kubectl rollout status deployment/cw2-server"
                    '''
                }
            }
        }
    }
    
    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
