pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
        DOCKERHUB_REPO        = 'defaultsaviour/runcalc-pro'
        IMAGE_TAG             = "v1.0.${BUILD_NUMBER}"
    }

    stages {

        stage('Get Code') {
            steps {
                echo "Checking out source code..."
                checkout scm
                echo "Checked out commit: ${env.GIT_COMMIT}"
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker image: ${DOCKERHUB_REPO}:${IMAGE_TAG}"
                sh "docker build -t ${DOCKERHUB_REPO}:${IMAGE_TAG} ."
            }
        }

        stage('Tag Image') {
            steps {
                echo "Tagging image as latest..."
                sh "docker tag ${DOCKERHUB_REPO}:${IMAGE_TAG} ${DOCKERHUB_REPO}:latest"
            }
        }

        stage('Push to Docker Hub') {
            steps {
                echo "Logging in to Docker Hub and pushing..."
                sh "echo ${DOCKERHUB_CREDENTIALS_PSW} | docker login -u ${DOCKERHUB_CREDENTIALS_USR} --password-stdin"
                sh "docker push ${DOCKERHUB_REPO}:${IMAGE_TAG}"
                sh "docker push ${DOCKERHUB_REPO}:latest"
            }
        }

    }

    post {
        always {
            sh "docker logout"
        }
        success {
            echo "CI complete. Image pushed as ${DOCKERHUB_REPO}:${IMAGE_TAG}"
        }
        failure {
            echo "CI pipeline failed — check the logs above."
        }
    }
}
