pipeline {
    agent { 
      docker {
         image 'maven:3-alpine'
         args '-v /root/.m2:/root/.m2'

      }
   }
stages {

   stage('Build') {
     steps {
        sh 'mvn -B -DskipTests clean package'
     }
   }
   stage('Test') {
    steps {

      sh 'mvn test'
    }

    post {
     always {
        junit 'target/surefire-reports/*.xml'
      }
   }

   }
 stage('Deliver') {
   steps {
    sh './jenkins/scripts/deliver.sh'
   }
     post{
         always{
              s3Upload consoleLogLevel: 'INFO', dontSetBuildResultOnFailure: false, dontWaitForConcurrentBuildCompletion: false, entries: [[bucket: 'myjavaapps3', excludedFile: '', flatten: true, gzipFiles: false, keepForever: true, managedArtifacts: true, noUploadOnFailure: true, selectedRegion: 'us-east-1', showDirectlyInBrowser: true, sourceFile: 'target', storageClass: 'STANDARD', uploadFromSlave: false, useServerSideEncryption: false]], pluginFailureResultConstraint: 'FAILURE', profileName: 'devops-jenkins81', userMetadata: [
         }
     
     }
     
  }
}
}
