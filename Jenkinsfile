pipeline {
    agent {
        docker {
            image 'maven:3-alpine'
            args '-v /root/.m2:/root/.m2'
        }
    }
    environment {
        GITHUB = credentials('gitHubCredentials')

    }

    stages {
        stage('Checkout') {
            steps{
                checkout scm
            }
        }
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

        stage('Install Docker') {
        when {
                branch 'master'
              }
        steps {
               sh ' docker run -d \
                    --name my-jenkins \
                    -v /var/jenkins_home:~/.jenkins \
                    -v /var/run/docker.sock:/var/run/docker.sock \
                    -p 8080:8080 jenkins'
               }

        }
        stage('Build Docker Image') {
                            when {
                                branch 'master'
                            }

                            steps {
                                echo '=== Building simple-java-maven-app Docker Image ==='
                                script {
                                    app = docker.build("rpidugu/simple-java-maven-app")
                                }
                            }
                }
                stage('Push Docker Image') {
                            when {
                                branch 'master'
                            }
                            steps {
                                echo '=== Pushing simple-java-maven-app Docker Image ==='
                                script {
                                    GIT_COMMIT_HASH = sh (script: "git log -n 1 --pretty=format:'%H'", returnStdout: true)
                                    SHORT_COMMIT = "${GIT_COMMIT_HASH[0..7]}"
                                    docker.withRegistry('https://registry.hub.docker.com', 'dockerHubCredentials') {
                                        app.push("$SHORT_COMMIT")
                                        app.push("latest")
                                    }
                                }
                            }
                }
                stage('Remove local images') {
                            steps {
                                echo '=== Delete the local docker images ==='
                                sh("docker rmi -f rpidugu/simple-java-maven-app:latest || :")
                                sh("docker rmi -f rpidugu/simple-java-maven-app:$SHORT_COMMIT || :")
                }
            }
    }
}
