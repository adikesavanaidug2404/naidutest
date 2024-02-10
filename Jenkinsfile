pipeline {
    agent any
    environment {
        SONARQUBE_HOME = tool "sonarscanner"
    }
    stages {
        stage('clone from github(GitHubBuild)') {
            steps {
                git branch: 'master', url: 'https://github.com/adikesavanaidug2404/DeployProject.git'
            }
        }
        stage('Compile (MVN Comp)') {
            steps {
                sh "mvn compile"
            }
        }
        stage('Unit-Test(MVN Test)') {
            steps {
                sh "mvn test -DskipTests=true"
            }
        }
        stage('Build war file(Maven Build)') {
            steps {
                sh "mvn clean install package"
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh "${SONARQUBE_HOME}/bin/sonar-scanner -Dsonar.projectKey=NaiduPro -Dsonar.projectName=NaiduPro -Dsonar.java.binaries=."
                }
            }
        }
        stage('Deploy to Tomcat') {
            steps {
                sh "sudo scp -o StrictHostKeyChecking=no /var/lib/jenkins/workspace/naidu/webapp/target/webapp.war root@54.160.138.39:/opt/tomcat/webapps/"
            }
        }
        stage('Docker Image Build&Tag') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'dockertoken', toolName: 'docker') {
                        sh "docker build -t adikesavanaidug2404/naiduproject:latest -f Dockerfile ."
                    }
                }
            }
        }
        stage('Docker Image Push') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'dockertoken', toolName: 'docker') {
                        sh "docker push adikesavanaidug2404/naiduproject:latest"
                    }
                }
            }
        }
    }
}
