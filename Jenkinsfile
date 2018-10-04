pipeline {

  agent any

  parameters {
    string(name: 'tomcat_dev', defaultValue: 'ec2-user@ec2-18-222-138-93.us-east-2.compute.amazonaws.com', description: 'Staging Server')
    string(name: 'tomcat_prod', defaultValue: 'ec2-user@ec2-18-191-11-135.us-east-2.compute.amazonaws.com', description: 'Production Server')
  }

  triggers {
   pollSCM('* * * * *')
  }

  tools {
    maven 'localMaven'
  }

  stages{
    stage('Build') {
      steps {
        sh 'mvn clean package'
      }
      post {
        success {
          echo 'Now Archiving...'
          archiveArtifacts artifacts: '**/target/*.war'
        }
      }
    }

    stage ('Deployments') {
      parallel {
        stage ('Deploy to Staging') {
          steps {
            sh "scp -i C:/Users/psuriya/Downloads/tomcat-demo.pem **/target/*.war ec2-user@${params.tomcat_dev}:/var/lib/tomcat7/webapps"
            echo "y"
          }
        }

        stage ("Deploy to Production") {
          steps {
            sh "scp -i C:/Users/psuriya/Downloads/tomcat-demo.pem **/target/*.war ec2-user@${params.tomcat_prod}:/var/lib/tomcat7/webapps"
            echo "y"
          }
        }
      }
    }
  }
}