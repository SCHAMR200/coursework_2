pipeline {
    agent any

    environment {
        registry = "schamr200/coursework2"
        registryCredential = 'dockerhub'
    }

    stages {
        stage('Clone Git Repo') {
          steps {
            git 'https://github.com/SCHAMR200/coursework_2.git'
          }
        }
        stage('Build Docker Image') {
          steps {
			      script {
                dockerImage = docker.build registry + ":$BUILD_NUMBER"
              }
            }
        }
		    stage('Deploy Docker Image') {
          steps{
            script {
              docker.withRegistry( '', registryCredential ) {
                dockerImage.push()
              }
            }
          }
        }
        stage('Run Sonarqube Tests') {
          environment {
           scannerHome = tool 'SonarQubeScanner'
          }
          steps {
            withSonarQubeEnv('SonarQubeScanner') {
                sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=server-jenkins-sonar-js -Dsonar.sources=."
            }
          }
        }

	      stage('Update application'){
		      steps{
		        sleep (15)
		        sh 'ssh schamr200@40.76.87.104 kubectl set image deployments/coursework2 coursework2=schamr200/coursework2:$BUILD_NUMBER'
		      }
        }
      }
}