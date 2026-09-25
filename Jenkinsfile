pipeline {

    agent any

    environment {
        RELEASE = "1.0.0"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"

        REPO_URL = 'https://github.com/IamAbii/DEVOPS_Complete.git'

        DOCKER_USER = 'abhilash2'

        FRONTEND_IMAGE = "${DOCKER_USER}/frontend-app"
        BACKEND_IMAGE = "${DOCKER_USER}/backend-app"
    }

    stages {

        stage('Cleanup Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout Code') {
            steps {
                git(
                    branch: 'main',
                    credentialsId: 'github',
                    url: REPO_URL
                )
            }
        }

        stage('Build Frontend') {
            steps {
                sh '''
                    cd frontend
                    npm install
                    npm run build
                '''
            }
        }

        stage('Build Backend') {
            steps {
                sh '''
                    cd backend
                    pip install -r requirements.txt
                '''
            }
        }

        stage('Build & Push Docker Images') {
            steps {
                script {

                    docker.withRegistry(
                        'https://index.docker.io/v1/',
                        'DOCKERHUB_CREDENTIALS'
                    ) {

                        def frontendImage = docker.build(
                            "${FRONTEND_IMAGE}:${IMAGE_TAG}",
                            "./frontend"
                        )

                        def backendImage = docker.build(
                            "${BACKEND_IMAGE}:${IMAGE_TAG}",
                            "./backend"
                        )

                        frontendImage.push()
                        backendImage.push()
                    }
                }
            }
        }
    }

    post {

        success {
            echo "Build succeeded!"
            echo "Build Number: ${BUILD_NUMBER}"
            echo "Image Tag: ${IMAGE_TAG}"
            echo "Build URL: ${BUILD_URL}"
        }

        failure {
            echo "Build failed!"
            echo "Check the console output: ${BUILD_URL}"
        }

        always {
            echo "Pipeline execution completed."
        }
    }
}
