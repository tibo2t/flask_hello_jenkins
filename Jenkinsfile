pipeline {
  agent {
    triggers {
 pollSCM('* * * * *')
 }
    kubernetes {
      // this label will be the prefix of the generated pod's name
      label 'jenkins-agent-my-app'
      yaml """
apiVersion: v1
kind: Pod
metadata:
  labels:
    component: ci
spec:
  containers:
    - name: python
      image: python:3.7
      command:
        - cat
      tty: true

    - name: docker
      image: docker
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

    }
  }
 

  stages {
    stage('Test python') {
      steps {
        container('python') {
          sh "pip install -r requirements.txt"
          sh "python test.py"
        }
     }
  }
  stage('Build image') {
    steps {
      container('docker') {
        sh "docker build -t localhost:4000/pythontest:latest ."
        sh "docker push localhost:4000/pythontest:latest"
       }

     
    }
  }

  stage('Deploy') {
    steps {
	container('kubectl') {
	  sh "kubectl apply -f /home/tjubert/tp_jenkins/deployment.yml"
	  sh "kubectl apply -f /home/tjubert/tp_jenkins/service.yml"
	}
     }
 }


}

