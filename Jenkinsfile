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
    
     
  }
    stage("publish to s3") {
    step([
        $class: 'S3BucketPublisher',
        entries: [[
            sourceFile: 'target/*.jar',
            bucket: 'myjavaapps3',
            selectedRegion: 'us-east-1',
            noUploadOnFailure: true,
            managedArtifacts: true,
            flatten: true,
            showDirectlyInBrowser: true,
            keepForever: true,
        ]],
        profileName: 'myprofile',
        dontWaitForConcurrentBuildCompletion: false, 
    ])
}

    
    
}
}
