pipeline {

  agent any

  parameters {
    string(name: 'tomcat_dev', defaultValue: 'ec2-18-217-189-142.us-east-2.compute.amazonaws.com', description: 'Staging Server')
    string(name: 'tomcat_prod', defaultValue: 'ec2-18-188-245-111.us-east-2.compute.amazonaws.com', description: 'Production Server')
    string(name: 'privateKey', defaultValue: 'C:/Users/psuriya/Downloads/tomcat-demo.ppk', description: 'private key path')
    string(name: 'destinationPath', defaultValue: '/var/lib/tomcat7/webapps/', description: 'Destination path')
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
        bat 'mvn clean package'
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
            bat 'winscp /command "open sftp://ec2-user@%tomcat_dev% -privatekey=%privateKey% -hostkey=*" "put ""C:\\Program Files (x86)\\Jenkins\\jobs\\FullyAutomatedPipeline\\builds\\%BUILD_NUMBER%\\archive/webapp\\target\\*.war"" %destinationPath%" "exit"'
          }
        }

        stage ("Deploy to Production") {
          steps {
            bat 'winscp /command "open sftp://ec2-user@%tomcat_prod% -privatekey=%privateKey% -hostkey=*" "put ""C:\\Program Files (x86)\\Jenkins\\jobs\\FullyAutomatedPipeline\\builds\\%BUILD_NUMBER%\\archive/webapp\\target\\*.war"" %destinationPath%" "exit"'
          }
        }
      }
    }
  }
}