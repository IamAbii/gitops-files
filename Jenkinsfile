pipeline {
    agent { label "Jenkins-Agent" }
    environment {
        FRONTEND_IMAGE = "abhilash2/frontend-app"
        BACKEND_IMAGE  = "abhilash2/backend-app"
        GIT_REPO_URL = "https://github.com/IamAbii/gitops-files.git"
        GIT_USER = "IamAbii"
        GIT_EMAIL = "abhihasankar2@gmail.com"
    }
    parameters {
        string(name: 'IMAGE_TAG', description: 'Docker image tag to deploy (format: 1.0.0-BUILD_NUMBER)')
    }
    stages {
        stage("Validate Parameters") {
            steps {
                script {
                    if (!params.IMAGE_TAG || params.IMAGE_TAG == "") {
                        error "IMAGE_TAG parameter is required but was not provided or is empty!"
                    }
                    echo "Will deploy image tag: ${params.IMAGE_TAG}"
                }
            }
        }
        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }
        stage("Checkout GitOps Repo") {
            steps {
                // Using withCredentials to authenticate git clone
                withCredentials([string(credentialsId: 'github-token', variable: 'GIT_TOKEN')]) {
                    sh """
                        git clone https://${GIT_USER}:${GIT_TOKEN}@github.com/IamAbii/gitops-files.git .
                        git checkout main
                    """
                }
            }
        }
        stage("Update Frontend Deployment Tag") {
            steps {
                sh """
                    echo "Before update - frontend.yaml"
                    cat frontend.yaml
                    
                    echo "Using image tag: ${params.IMAGE_TAG}"
                    sed -i "s|${FRONTEND_IMAGE}:.*|${FRONTEND_IMAGE}:${params.IMAGE_TAG}|g" frontend.yaml
                    
                    echo "After update - frontend.yaml"
                    cat frontend.yaml
                """
            }
        }
        stage("Update Backend Deployment Tag") {
            steps {
                sh """
                    echo "Before update - backend.yaml"
                    cat backend.yaml
                    
                    echo "Using image tag: ${params.IMAGE_TAG}"
                    sed -i "s|${BACKEND_IMAGE}:.*|${BACKEND_IMAGE}:${params.IMAGE_TAG}|g" backend.yaml
                    
                    echo "After update - backend.yaml"
                    cat backend.yaml
                """
            }
        }
        stage("Commit and Push Changes to Git") {
            steps {
                sh """
                    git config --global user.name "${GIT_USER}"
                    git config --global user.email "${GIT_EMAIL}"
                    git add frontend.yaml backend.yaml
                """
                
                // Only commit if there are changes
                sh """
                    if git diff --cached --exit-code; then
                        echo "No changes to commit"
                    else
                        git commit -m "Updated image tags to ${params.IMAGE_TAG}"
                    fi
                """
                
                // Push using the token credential
                withCredentials([string(credentialsId: 'github-token', variable: 'GIT_TOKEN')]) {
                    sh """
                        git remote set-url origin https://${GIT_USER}:${GIT_TOKEN}@github.com/IamAbii/gitops-files.git
                        echo "Pushing changes to repository with tag ${params.IMAGE_TAG}..."
                        git push origin main
                    """
                }
            }
        }
    }
    post {
        success {
            echo "Successfully updated deployment files with image tag: ${params.IMAGE_TAG}"
        }
        failure {
            echo "Failed to update deployment files. Please check the logs for details."
        }
    }
}
