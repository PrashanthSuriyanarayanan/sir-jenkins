pipeline {

  agent any

  parameters {
    string(name: 'tomcat_dev', defaultValue: 'ec2-user@ec2-18-222-138-93.us-east-2.compute.amazonaws.com', description: 'Staging Server')
    string(name: 'tomcat_prod', defaultValue: 'ec2-user@ec2-18-191-11-135.us-east-2.compute.amazonaws.com', description: 'Production Server')
    string(name: 'source', defaultValue: 'C:/Program Files (x86)/Jenkins/jobs/FullyAutomatedPipeline/builds/${BUILD_NUMBER}/archive/webapp/target/*.war', description: 'Source path')
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
            echo 'y' | bat 'winscp /command "open sftp://${params.tomcat_dev} -privatekey=${params.privateKey}" "put ${params.source} ${params.destinationPath}" "exit"'
          }
        }

        stage ("Deploy to Production") {
          steps {
            bat 'winscp /command "open sftp://${params.tomcat_prod} -privatekey=${params.privateKey} -hostkey=*" "put ${params.source} ${params.destinationPath}" "exit"'
          }
        }
      }
    }
  }
}