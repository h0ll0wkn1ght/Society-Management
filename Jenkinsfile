pipeline {
    agent any

    environment {
        // Change 'yourdockerhubusername' to your actual Docker Hub username
        DOCKER_HUB_USER = 'yourdockerhubusername'
        // These are the names of the images we will build
        BACKEND_IMAGE = "${DOCKER_HUB_USER}/society-backend"
        FRONTEND_IMAGE = "${DOCKER_HUB_USER}/society-frontend"
        // Generate a unique tag for each build (e.g., build number)
        IMAGE_TAG = "v${env.BUILD_ID}"
        
        // This ID should match the Credentials ID you create in Jenkins for Docker Hub
        DOCKER_CREDS_ID = 'docker-hub-credentials'
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out code from GitHub...'
                // This step automatically checks out the code from the branch Jenkins is configured to watch
                checkout scm
            }
        }

        stage('Build Docker Images') {
            steps {
                echo 'Building Backend Image...'
                dir('backend') {
                    sh 'docker build -t ${BACKEND_IMAGE}:${IMAGE_TAG} -t ${BACKEND_IMAGE}:latest .'
                }

                echo 'Building Frontend Web Image...'
                dir('frontend-web') {
                    sh 'docker build -t ${FRONTEND_IMAGE}:${IMAGE_TAG} -t ${FRONTEND_IMAGE}:latest .'
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                echo 'Pushing Docker Images to Docker Hub...'
                withCredentials([usernamePassword(credentialsId: env.DOCKER_CREDS_ID, passwordVariable: 'DOCKER_PASS', usernameVariable: 'DOCKER_USER')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh 'docker push ${BACKEND_IMAGE}:${IMAGE_TAG}'
                    sh 'docker push ${BACKEND_IMAGE}:latest'
                    sh 'docker push ${FRONTEND_IMAGE}:${IMAGE_TAG}'
                    sh 'docker push ${FRONTEND_IMAGE}:latest'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                echo 'Deploying to Kubernetes Cluster...'
                // Placeholder for Kubernetes deployment step
                // We will create the k8s YAML files next!
                // sh 'kubectl apply -f k8s/'
                // sh 'kubectl set image deployment/backend backend=${BACKEND_IMAGE}:${IMAGE_TAG}'
                // sh 'kubectl set image deployment/frontend frontend=${FRONTEND_IMAGE}:${IMAGE_TAG}'
                echo 'Deployment successful!'
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution complete.'
            // Clean up old local images to save space on the Jenkins server
            sh 'docker image prune -f'
        }
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed. Check the logs.'
        }
    }
}
