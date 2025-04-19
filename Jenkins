pipeline {
    agent { label "Jenkins-Agent" }

    environment {
        FRONTEND_IMAGE = "abhilash2/frontend-app"
        BACKEND_IMAGE  = "abhilash2/backend-app"
        GIT_REPO_URL = "https://github.com/IamAbii/gitops-files.git"
        GIT_CREDENTIALS_ID = "github"
        GIT_USER = "IamAbii"
        GIT_EMAIL = "abhihasankar2@gmail.com"
    }

    stages {
        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }

        stage("Checkout GitOps Repo") {
            steps {
                git branch: 'main', credentialsId: "${GIT_CREDENTIALS_ID}", url: "${GIT_REPO_URL}"
            }
        }

        stage("Update Frontend Deployment Tag") {
            steps {
                sh """
                    echo "Before update - frontend.yaml"
                    cat gitops-files/frontend.yaml
                    
                    sed -i "s|${FRONTEND_IMAGE}:.*|${FRONTEND_IMAGE}:${IMAGE_TAG}|g" gitops-files/frontend.yaml

                    echo "After update - frontend.yaml"
                    cat gitops-files/frontend.yaml
                """
            }
        }

        stage("Update Backend Deployment Tag") {
            steps {
                sh """
                    echo "Before update - backend.yaml"
                    cat gitops-files/backend.yaml
                    
                    sed -i "s|${BACKEND_IMAGE}:.*|${BACKEND_IMAGE}:${IMAGE_TAG}|g" gitops-files/backend.yaml

                    echo "After update - backend.yaml"
                    cat gitops-files/backend.yaml
                """
            }
        }

        stage("Commit and Push Changes to Git") {
            steps {
                sh """
                    git config --global user.name "${GIT_USER}"
                    git config --global user.email "${GIT_EMAIL}"
                    git add gitops-files/frontend.yaml gitops-files/backend.yaml
                    git commit -m "Updated image tags to ${IMAGE_TAG}"
                """
                withCredentials([gitUsernamePassword(credentialsId: "${GIT_CREDENTIALS_ID}", gitToolName: 'Default')]) {
                    sh 'git push https://github.com/IamAbii/gitops-files.git main'
                }
            }
        }
    }
}
