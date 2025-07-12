pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'narenkanugu/html-app'
        CREDENTIALS_ID = 'dockerhub'
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/Narendarkanugu/narendarkanugu.github.io.git'
            }
        }

        stage('Get Git Commit Hash') {
            steps {
                script {
                    COMMIT_HASH = sh(script: "git rev-parse --short HEAD", returnStdout: true).trim()
                    env.IMAGE_TAG = "build-${env.BUILD_NUMBER}"
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE:$IMAGE_TAG .'
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: "$CREDENTIALS_ID", usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh 'echo "$PASSWORD" | docker login -u "$USERNAME" --password-stdin'
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                sh 'docker push $DOCKER_IMAGE:$IMAGE_TAG'
            }
        }

        stage('Tag as latest') {
            steps {
                sh """
                docker tag $DOCKER_IMAGE:$IMAGE_TAG $DOCKER_IMAGE:latest
                docker push $DOCKER_IMAGE:latest
                """
            }
        }

        stage('Logout Docker') {
            steps {
                sh 'docker logout'
            }
        }

        stage('Deploy') {
            steps {
                sh """
                docker rm -f html-app || true
                docker run -d -p 8085:80 --name html-app $DOCKER_IMAGE:$IMAGE_TAG
                """
            }
        }
    }
}
