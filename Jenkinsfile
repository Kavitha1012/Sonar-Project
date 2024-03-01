pipeline{
    agent any
    environment {
        PATH = "$PATH:/opt/apache-maven-3.9.6/bin"
    }
    stages{
       stage('Checkout'){
            steps{
                git 'git@github.com:Kavitha1012/Sonar-Project.git'
            }
         }        
       stage('Package'){
            steps{
                sh 'mvn clean package'
            }
         }
	     stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                success {
                    junit 'target/surefire-reports/**/*.xml' 
        
		        }
		  }
        }
        stage('SonarQube analysis') {
//    def scannerHome = tool 'SonarScanner 4.0';
        steps{
        withSonarQubeEnv('SonarQube') { 
        // If you have configured more than one global server connection, you can specify its name
//      sh "${scannerHome}/bin/sonar-scanner"
        sh "mvn clean verify sonar:sonar -Dsonar.projectKey=sonar-project -Dsonar.host.url=http://52.66.248.50:9000 -Dsonar.login=sqp_c8464335805beb8ab4bef5a3c56915a9cc97f628"
    }
        }
        }
       
    stage ('Deploy to Nexus') {
            steps {
      nexusArtifactUploader(
      nexusVersion: 'nexus3',
      protocol: 'http',
      nexusUrl: '13.233.25.187:8081/',
      groupId: 'myGroupId',
      version: '1.0-SNAPSHOT',
      repository: 'maven-snapshots',
      credentialsId: 'nexuscredentials',
      artifacts: [
      [artifactId: 'maven-project',
      classifier: '',
      file: 'webapp/target/webapp.war',
      type: 'war']
      ])
      }
        }
        stage ('Deploy to Prod'){
     steps {
        sh 'scp -o StrictHostKeyChecking=no webapp/target/webapp.war root@13.233.33.45:/opt/apache-tomcat-8.5.99/webapps'
           }
   }
   }
   post {
        success {
            emailext (
                subject: "Pipeline Success: ${env.JOB_NAME}",
                body: "The pipeline ${env.JOB_NAME} has successfully completed.",
                recipientProviders: [[$class: 'DevelopersRecipientProvider']],
                to: "rishikumar11210@gmail.com",
                replyTo: "rishikumar11210@gmail.com",
                from: "rishikumar11210@gmail.com",
                attachLog: true,
                compressLog: true
            )
        }
        failure {
            emailext (
                subject: "Pipeline Failure: ${env.JOB_NAME}",
                body: "The pipeline ${env.JOB_NAME} has failed. Please check the Jenkins console output for more details.",
                recipientProviders: [[$class: 'DevelopersRecipientProvider']],
                to: "rishikumar11210@gmail.com",
                replyTo: "rishikumar11210@gmail.com",
                from: "rishikumar11210@gmail.com",
                attachLog: true,
                compressLog: true
            )
        }
    }   
}
