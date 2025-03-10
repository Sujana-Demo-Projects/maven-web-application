pipeline{
    agent any
    tools {
        maven 'Maven-3.8.6'
        
    }
    options {
        buildDiscarder logRotator(artifactDaysToKeepStr: '3', artifactNumToKeepStr: '3', daysToKeepStr: '3', numToKeepStr: '3')
        timestamps()
    }
    stages{
        stage("CheckOutCodeFromGitHub"){
            steps{
                git 'https://github.com/Sujana-Demo-Projects/maven-web-application.git'
            }
        }
        stage("BuildingPackage"){
            steps{
                sh "mvn clean package"
            }
        }
        stage("SourceCodeQualityTestingUsingSonarQube"){
            steps{
                withSonarQubeEnv('sonarqubejenkins') {
                    sh 'mvn sonar:sonar'
                } 
            }
            
        }
        stage("Quality Gate") {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                waitForQualityGate abortPipeline: true
                }
            }
          }
        stage("StoringPackageInJFrog"){
            steps{
                sh "mvn deploy"
            }
        }
        stage("DeployingInTomcat"){
            steps{
                sshagent(['3a742398-997a-4d45-ae85-b016ec58df9b']) {
                    sh "scp -o StrictHostKeyChecking=no target/maven-web-applications.war ec2-user@43.204.97.177:/opt/apache-tomcat-9.0.65/webapps/ "
                }
                    
            }
        }
    }
}
