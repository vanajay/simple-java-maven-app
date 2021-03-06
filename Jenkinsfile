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
    stage('updload to s3'){
        steps {
          withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', accessKeyVariable: 'AKIAZMBOBHYWPSE7BTUO', credentialsId: 'deploytos3', secretKeyVariable: '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']]) {
          
          sh 'curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"'
          sh 'apt install zip unzip -y'     
          sh 'unzip awscliv2.zip'
          sh './aws/install'
              
          sh 'aws s3 ls'
              
        }
    
        
    }
  }
   
    
    
}
}
