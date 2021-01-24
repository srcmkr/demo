pipeline {
  environment {
    registry = "username/repository"
    dockerImage = ''
  }
  agent any
  stages {
    stage('Cloning Git') {
      steps {
        git branch: 'master', credentialsId: 'gitcred', url: 'https://github.com/<url>.git'
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
          docker.withRegistry( 'https://d3v.to', 'haborcred' ) {
            dockerImage.push()
          }
        }
      }
    }
	stage('Deploy on kubernetes') {
		steps {
			kubernetesDeploy(
				kubeconfigId: 'k8s-default-namespace-config-id',
				configs: 'deployment.yml',
				enableConfigSubstitution: true
			  )
		  }
	  }
  }
}
