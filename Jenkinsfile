pipeline {
  agent any

  environment {
    IMAGE_NAME = "6065meet/devops-journey"
    KUBE_NAMESPACE = "devops-site"
  }

  stages {
    stage('Build Docker Image') {
      steps {
        sh 'docker build -t $IMAGE_NAME ./devops-site'
      }
    }

    stage('Push to DockerHub') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
          sh '''
            echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
            docker push $IMAGE_NAME
          '''
        }
      }
    }

    stage('Deploy to Kubernetes') {
      steps {
        sh '''
          kubectl set image deployment/devops-site devops-site=$IMAGE_NAME -n $KUBE_NAMESPACE
        '''
      }
    }
  }
}
