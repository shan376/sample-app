pipeline {
  agent any
  environment {
    DOCKERHUB_USER = "shanzafar046"
    IMAGE_NAME = "sample-app"
  }
  stages {
    stage('Checkout') {
      steps {
        // Correct branch specify karo
        git branch: 'main', url: 'https://github.com/shan376/sample-app.git'
      }
    }
    stage('Build Docker Image') {
      steps {
        sh 'docker build -t $DOCKERHUB_USER/$IMAGE_NAME:$BUILD_NUMBER .'
      }
    }
    stage('Push to DockerHub') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
          sh 'echo "$PASS" | docker login -u "$USER" --password-stdin'
          sh 'docker push $DOCKERHUB_USER/$IMAGE_NAME:$BUILD_NUMBER'
        }
      }
    }
    stage('Deploy to Kubernetes') {
      steps {
        sh 'kubectl set image deployment/$IMAGE_NAME $IMAGE_NAME=$DOCKERHUB_USER/$IMAGE_NAME:$BUILD_NUMBER'
      }
    }
    stage('Verify Rollout') {
      steps {
        sh 'kubectl rollout status deployment/$IMAGE_NAME'
      }
    }
  }
}
