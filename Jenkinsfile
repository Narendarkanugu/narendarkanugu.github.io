pipeline {
  agent any

  environment {
    IMAGE_NAME = "narenkanugu/html-app"
    VERSION = "v1.0.${BUILD_NUMBER}"
  }

  stages {
    stage('Checkout') {
      steps {
        git 'https://github.com/Narendarkanugu/narendarkanugu.github.io.git'
      }
    }

    stage('Build Docker Image') {
      steps {
        sh '''
          echo "Building Docker image: $IMAGE_NAME:$VERSION"
          docker build -t $IMAGE_NAME:$VERSION .
          docker tag $IMAGE_NAME:$VERSION $IMAGE_NAME:latest
        '''
      }
    }

    stage('Push to Docker Hub') {
      steps {
        withCredentials([usernamePassword(
          credentialsId: 'dockerhub',
          usernameVariable: 'DOCKER_USER',
          passwordVariable: 'DOCKER_PASS'
        )]) {
          sh '''
            echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
            docker push $IMAGE_NAME:$VERSION
            docker push $IMAGE_NAME:latest
          '''
        }
      }
    }

    stage('Deploy') {
      steps {
        sh '''
          docker rm -f html-app || true
          docker run -d -p 8085:80 --name html-app $IMAGE_NAME:$VERSION
        '''
      }
    }
  }
}
