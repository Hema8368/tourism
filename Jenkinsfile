pipeline {
  agent any

  environment {
    DOCKERHUB_CREDENTIALS = 'dockerhub-creds'
    IMAGE_NAME = 'hsindhuja/tourism'
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build Image') {
      steps {
        sh '''
          set -eux
          docker version
          docker build -t $IMAGE_NAME:${BUILD_NUMBER} -t $IMAGE_NAME:latest .
        '''
      }
    }

    stage('Push Image') {
      steps {
        withCredentials([usernamePassword(
          credentialsId: "${DOCKERHUB_CREDENTIALS}",
          usernameVariable: 'DH_USER',
          passwordVariable: 'DH_PASS'
        )]) {
          sh '''
            set -eux
            echo "$DH_PASS" | docker login -u "$DH_USER" --password-stdin
            docker push $IMAGE_NAME:${BUILD_NUMBER}
            docker push $IMAGE_NAME:latest
            docker logout
          '''
        }
      }
    }
  }

  post {
    always {
      sh 'docker image prune -f || true'
    }
  }
}
