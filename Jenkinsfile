pipeline {
    agent{
      label 'dev'
    tools {
        jdk 'jdk21'
        nodejs 'node16'
    }
    environment {
        SCANNER_HOME=tool 'mysonar'
    }
    stages {
        stage("Clean Workspace"){
            steps{
                cleanWs()
            }
        }
        stage("Code Checkout"){
            steps{
                git "https://github.com/sumanthtony/DevSecOps-Zomato-Project.git"
            }
        }
        stage("SonarQube - CQA"){
            steps{
                withSonarQubeEnv('mysonar') {
                    sh "${SCANNER_HOME}/bin/sonar-scanner -Dsonar.projectKey=Swiggy -Dsonar.projectName=Swiggy"
                }
            }
        }
        stage("Quality Gates"){
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'NEW-SONAR-TOKEN'
                }
            }
        }
    }
}
