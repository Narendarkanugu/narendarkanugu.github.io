pipeline {
    agent any

    environment {
        IMAGE_NAME = 'html-app'
        CONTAINER_NAME = 'html-app'
        PORT = '8085'
    }

    stages {
        stage('Clone Code') {
            steps {
                git 'https://github.com/Narendarkanugu/narendarkanugu.github.io.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t $IMAGE_NAME ."
            }
        }

        stage('Remove Existing Container') {
            steps {
                sh "docker rm -f $CONTAINER_NAME || true"
            }
        }

        stage('Run Container') {
            steps {
                sh "docker run -d -p $PORT:80 --name $CONTAINER_NAME $IMAGE_NAME"
            }
        }
    }
}
