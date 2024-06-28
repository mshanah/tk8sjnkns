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
        script {
          sh 'docker build -t 1771985/react-app .'
        }
      }
    }
    stage('Pushing Image') {
      environment {
          registryCredential = 'dhc'
           }
      steps{
        script {
          docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
            dockerImage.push("latest")
          }
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
