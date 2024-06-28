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
          sh 'docker login -u ${env.dockerHubUser} -p "${env.dockerHubPassword}"'
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
          kubernetesDeploy(configs: "deployment.yaml", 
                                         "service.yaml","05.yaml")
        }
      }
    }
  }
}
