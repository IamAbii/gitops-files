pipeline {
    agent any

    environment {
        FRONTEND_IMAGE = "abhilash2/frontend-app"
        BACKEND_IMAGE  = "abhilash2/backend-app"
        GIT_USER       = "IamAbii"
        GIT_EMAIL      = "abhilashhasankar2@gmail.com"
    }

    parameters {
        string(
            name: 'IMAGE_TAG',
            defaultValue: '1.0.0-2',
            description: 'Docker image tag to deploy (format: 1.0.0-BUILD_NUMBER)'
        )
    }

    stages {

        stage("Validate Parameters") {
            steps {
                script {
                    if (!params.IMAGE_TAG?.trim()) {
                        error "IMAGE_TAG parameter is required but was not provided or is empty!"
                    }

                    echo "Will deploy image tag: ${params.IMAGE_TAG}"
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
                    git config user.name "${GIT_USER}"
                    git config user.email "${GIT_EMAIL}"

                    git add frontend.yaml backend.yaml
                """

                script {
                    def changes = sh(
                        script: "git diff --cached --quiet",
                        returnStatus: true
                    )

                    if (changes == 0) {
                        echo "No changes to commit."
                    } else {

                        sh """
                            git commit -m "Updated image tags to ${params.IMAGE_TAG}"
                        """

                        withCredentials([
                            usernamePassword(
                                credentialsId: 'github',
                                usernameVariable: 'GITHUB_USER',
                                passwordVariable: 'GIT_TOKEN'
                            )
                        ]) {
                            sh '''
                                set +x

                                git remote set-url origin \
                                "https://${GITHUB_USER}:${GIT_TOKEN}@github.com/IamAbii/gitops-files.git"

                                echo "Pushing GitOps changes..."

                                git push origin HEAD:main
                            '''
                        }
                    }
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
