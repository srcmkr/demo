pipeline {
  environment {
    registry = "demo/demo"
    dockerImage = ''
  }
  agent any
  stages {
    stage('Cloning Git') {
      steps {
        git branch: 'master', credentialsId: 'gitcreds', url: 'https://github.com/srcmkr/demo.git'
      }
    }
    stage('Building image') {
      steps{  
	script {
	  dockerImage = docker.build registry + ":${currentBuild.number}"
	}
      }
    }
    stage('Deploy Image') {
      steps{
        script {
          docker.withRegistry( 'https://devops.d3v.to', 'harborcreds' ) {
            dockerImage.push()
          }
        }
      }
    }
    stage('Deploy on kubernetes') {
      steps {
	  yaml """
	  apiVersion: apps/v1
kind: Deployment
metadata:
  name: csharpdevops1-deployment
  labels:
    app: csharpdevops1
    role: rolling-update
spec:
  replicas: 4
  selector:
    matchLabels:
      app: csharpdevops1
  revisionHistoryLimit: 2
  strategy:
    type: RollingUpdate
  template:
    metadata:
      labels:
        app: csharpdevops1
    spec:
      containers:
      - name: csharpdevops1
        image: srcmkr/csharpdevopsdemo:${currentBuild.number}
        imagePullPolicy: Always
        env:
        - name: COLOR
          value: "#243554"
        ports:
        - containerPort: 80
        readinessProbe:
          httpGet:
            path: /
            port: 80
    """
       }
     }
  }
}
