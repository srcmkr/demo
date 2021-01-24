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
	  dockerImage = docker.build registry + ":latest"
	}
      }
    }
    stage('Deploy Image') {
      steps{
        script {
          docker.withRegistry( 'https://devops.d3v.to', 'haborcreds' ) {
            dockerImage.push()
          }
        }
      }
    }
	stage('Deploy on kubernetes') {
		steps {
			kubernetesDeploy(
				kubeconfigId: 'k8s-config',
				configs: 'deployment.yml',
				enableConfigSubstitution: true
			)
		}
	}
  }
}
