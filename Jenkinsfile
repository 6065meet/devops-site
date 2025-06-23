pipeline {
  agent {
    kubernetes {
      label 'docker-kubectl-agent'
      defaultContainer 'docker-kubectl'
      yaml """
apiVersion: v1
kind: Pod
metadata:
  labels:
    some-label: docker-kubectl-agent
spec:
  containers:
  - name: docker-kubectl
    image: docker:24.0.7-cli
    command:
    - sh
    - -c
    - >
      apk add --no-cache curl bash git &&
      curl -LO https://dl.k8s.io/release/v1.29.0/bin/linux/amd64/kubectl &&
      install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl &&
      sleep infinity
    tty: true
    volumeMounts:
    - mountPath: /var/run/docker.sock
      name: dockersock
    - mountPath: /home/jenkins/agent
      name: workspace-volume
  - name: jnlp
    image: jenkins/inbound-agent:latest
    volumeMounts:
    - mountPath: /home/jenkins/agent
      name: workspace-volume
  volumes:
  - name: dockersock
    hostPath:
      path: /var/run/docker.sock
  - name: workspace-volume
    emptyDir: {}
"""
    }
  }

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
            echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin || exit 1
            docker push $IMAGE_NAME
          '''
        }
      }
    }

    stage('Deploy to Kubernetes') {
      steps {
        sh 'kubectl set image deployment/devops-site devops-site=$IMAGE_NAME -n $KUBE_NAMESPACE'
      }
    }
  }
}
