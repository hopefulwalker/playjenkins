pipeline {

  environment {
    registry = "192.168.28.230:30400/walker/myweb"
    dockerImage = ""
	buildTag = ""
  }

  agent any

  stages {

    stage('Checkout Source') {
      steps {
        git 'https://github.com/hopefulwalker/playjenkins.git'
		script{
		  buildTag=sh(returnStdout: true, script: 'git rev-parse --short HEAD').trim()
		}
      }
    }

    stage('Build image') {
      steps{
        script {
          dockerImage = docker.build registry + ":${buildTag}"
        }
      }
    }

    stage('Push Image') {
      steps{
        script {
          docker.withRegistry( "" ) {
            dockerImage.push()
          }
        }
      }
    }

    stage('Deploy App') {
      steps {
        script {
		  sh "sed -i 's/<BUILD_TAG>/${buildTag}/' myweb.yaml"
          kubernetesDeploy(configs: "myweb.yaml", kubeconfigId: "mykubeconfig")
        }
      }
    }

  }

}
