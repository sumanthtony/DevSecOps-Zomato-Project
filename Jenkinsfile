pipeline {
    agent{
        label 'dev'
    }
    tools {
        jdk 'jdk21'
        nodejs 'node20'
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
            steps{
                timeout(time: 3, unit: 'MINUTES'){
                    waitForQualityGate abortPipeline: false
                }
            }
        }
        stage("NODE JS install"){
            steps{
                sh "npm install"
            }
        }
        stage("OWASP Dependency Check"){
            steps{
                dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit --noupdate', odcInstallation: 'Dp-Check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        stage("Docker Image Build"){
            steps{
                sh "docker build -t myimage ."
            }
        }
        stage("Trivy scan"){
            steps{
                sh "trivy fs . >> trivyfs.txt"
            }
        }
        stage("Scan Docker Image"){
            steps{
                sh "trivy image myimage"
            }
        }
        stage("Docker Build & Push to DockerHub"){
            steps{
                script{
                    withDockerRegistry(credentialsId: 'docker-credentials') {
                        sh 'docker tag myimage sumanthtony/zomato-application:latest'
                        sh 'docker push sumanthtony/zomato-application:latest'
}
                }
            }
        }
        stage("Deployed the Application"){
            steps{
                sh '''
                    docker stop cont-1 || true
                    docker rm cont-1 || true
                    docker run -d --name cont-1 -p 2222:3000 sumanthtony/zomato-application:latest
                '''
            }
        }
        
        
    }
}
