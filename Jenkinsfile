pipeline {
  environment {
    dockerimagename = "1771985/react-app"
    dockerImage = ""
  }
  agent {
    kubernetes {
       yaml '''
        apiVersion: v1
        kind: Pod
        spec:
          containers:
          - name: docker
            image: docker:latest
            command:
            - cat
            tty: true
            volumeMounts:
             - mountPath: /var/run/docker.sock
               name: docker-sock
          volumes:
          - name: docker-sock
            hostPath:
              path: /var/run/docker.sock    
        '''
    }
  }
  stages {
    stage('Checkout Source') {
      steps {
        git 'https://github.com/mshanah/tk8sjnkns.git'
      }
    }
    stage('Build image') {
      steps{
         container('docker') {
          sh 'docker build -t 1771985/react-app .'
        }
      }
    }
    stage('Login-Docker') {
      steps {
        container('docker') {
          withCredentials([usernamePassword(credentialsId: 'dhc', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
          sh 'docker login -u $dockerHubUser -p "$dockerHubPassword"'
          }
      }
    }
    }
    stage('Push-Images-Docker-to-DockerHub') {
      steps {
        container('docker') {
          sh 'docker push 1771985/react-app:latest'
      }
    }
     }
    stage('Deploying React.js container to Kubernetes') {
      steps {
        script {
          withKubeConfig ([credentialsId: 'mkc' , serverUrl: 'https://192.168.49.2:8080'])
          {
            sh 'kubectl apply -f deployment.yaml'
            sh 'kubectl apply -f service.yaml'
            sh 'kubectl apply -f 05.yaml'
          }
        }
      }
    }
  }
   post {
      always {
        container('docker') {
          sh 'docker logout'
      }
      }
    }
}
