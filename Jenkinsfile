pipeline {
    agent { label "Jenkins-Agent" }

    environment {
        FRONTEND_IMAGE = "abhilash2/frontend-app"
        BACKEND_IMAGE  = "abhilash2/backend-app"
        GIT_REPO_URL = "https://github.com/IamAbii/gitops-files.git"
        GIT_CREDENTIALS_ID = "github-token" // Your new credential ID here
        GIT_USER = "IamAbii"
        GIT_EMAIL = "abhihasankar2@gmail.com"
    }

    parameters {
        string(name: 'IMAGE_TAG', defaultValue: 'latest', description: 'Docker image tag to deploy')
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
                script {
                    if (params.IMAGE_TAG == null || params.IMAGE_TAG.trim() == '') {
                        error "IMAGE_TAG parameter is missing or empty"
                    }
                    
                    sh """
                        echo "Before update - frontend.yaml"
                        cat frontend.yaml
                        
                        sed -i 's|${FRONTEND_IMAGE}:.*|${FRONTEND_IMAGE}:${params.IMAGE_TAG}|g' frontend.yaml

                        echo "After update - frontend.yaml"
                        cat frontend.yaml
                    """
                }
            }
        }

        stage("Update Backend Deployment Tag") {
            steps {
                sh """
                    echo "Before update - backend.yaml"
                    cat backend.yaml
                    
                    sed -i 's|${BACKEND_IMAGE}:.*|${BACKEND_IMAGE}:${params.IMAGE_TAG}|g' backend.yaml

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
                    git commit -m "Updated image tags to ${params.IMAGE_TAG}"
                """
                
                withCredentials([string(credentialsId: "${GIT_CREDENTIALS_ID}", variable: 'TOKEN')]) {
                    sh '''
                        git push https://oauth2:${TOKEN}@github.com/IamAbii/gitops-files.git main
                    '''
                }
            }
        }
    }
}
