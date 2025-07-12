pipeline {
    agent any

    environment {
        IMAGE_NAME = 'html-app'
        DOCKERHUB_REPO = 'narenkanugu/html-app'
        CREDENTIALS_ID = 'dockerhub'  // must match Jenkins credentials ID
    }

    stages {
        stage('Checkout Code') {
            steps {
                git 'https://github.com/Narendarkanugu/narendarkanugu.github.io.git'
            }
        }

        stage('Get Commit Hash') {
            steps {
                script {
                    COMMIT_HASH = sh(script: "git rev-parse --short HEAD", returnStdout: true).trim()
                    env.IMAGE_TAG = "build-${env.BUILD_NUMBER}"
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
                sh 'docker tag $IMAGE_NAME $DOCKERHUB_REPO:$IMAGE_TAG'
                sh 'docker tag $IMAGE_NAME $DOCKERHUB_REPO:latest'
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: "${CREDENTIALS_ID}", usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh 'echo "$PASSWORD" | docker login -u "$USERNAME" --password-stdin'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                sh 'docker push $DOCKERHUB_REPO:$IMAGE_TAG'
                sh 'docker push $DOCKERHUB_REPO:latest'
            }
        }

        stage('Run Docker Container') {
            steps {
                sh '''
                    docker rm -f html-app || true
                    docker run -d -p 8085:80 --name html-app $DOCKERHUB_REPO:$IMAGE_TAG
                '''
            }
        }

        stage('Logout Docker') {
            steps {
                sh 'docker logout'
            }
        }
    }
}
