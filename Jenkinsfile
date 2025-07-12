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
    stage('Cleanup Old Images') {
  steps {
    sh '''
      echo "🧹 Keeping only the 5 most recent html-app Docker images..."

      IMAGE_NAME="narenkanugu/html-app"

      # Get all image IDs sorted by creation time (excluding latest tag)
      IMAGE_IDS=$(docker images $IMAGE_NAME --format "{{.ID}} {{.Repository}}:{{.Tag}}" | grep -v ":latest" | sort -u | awk '{print $1}')

      # Count total images
      TOTAL=$(echo "$IMAGE_IDS" | wc -l)

      # Calculate how many to delete
      DELETE_COUNT=$((TOTAL - 5))

      if [ "$DELETE_COUNT" -gt 0 ]; then
        echo "Removing $DELETE_COUNT old image(s)..."
        echo "$IMAGE_IDS" | head -n $DELETE_COUNT | xargs -r docker rmi -f
      else
        echo "No old images to delete."
      fi

      echo "✅ Docker cleanup complete."
    '''
  }
}

  }
}
