pipeline {
    agent { label "Jenkins-Agent" }

    environment {
        FRONTEND_APP_NAME = "abhilash2/frontend-app"
        BACKEND_APP_NAME  = "abhilash2/backend-app"
        IMAGE_TAG         = "1.0.0-${BUILD_NUMBER}" // example dynamic tag
    }

    stages {
        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }

        stage("Checkout from GitHub") {
            steps {
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/IamAbii/gitops-files.git'
            }
        }

        stage("Update Frontend Deployment Tag") {
            steps {
                sh """
                echo "Updating frontend.yaml with ${FRONTEND_APP_NAME}:${IMAGE_TAG}"
                sed -i 's|${FRONTEND_APP_NAME}:.*|${FRONTEND_APP_NAME}:${IMAGE_TAG}|g' frontend.yaml
                """
            }
        }

        stage("Update Backend Deployment Tag") {
            steps {
                sh """
                echo "Updating backend.yaml with ${BACKEND_APP_NAME}:${IMAGE_TAG}"
                sed -i 's|${BACKEND_APP_NAME}:.*|${BACKEND_APP_NAME}:${IMAGE_TAG}|g' backend.yaml
                """
            }
        }

        stage("Push Updated Manifests to GitHub") {
            steps {
                sh """
                git config --global user.name "IamAbii"
                git config --global user.email "abhihasankar2@gmail.com"
                git add frontend.yaml backend.yaml
                git commit -m "Updated frontend/backend image to ${IMAGE_TAG}"
                """
                withCredentials([gitUsernamePassword(credentialsId: 'github', gitToolName: 'Default')]) {
                    sh "git push https://github.com/IamAbii/gitops-files.git main"
                }
            }
        }
    }
}
