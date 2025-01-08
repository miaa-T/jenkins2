pipeline {

  agent any
   environment {
          deployStatus = ''
      }
    stages {
       stage("test") {
                          steps {
                              script {

                                      bat './gradlew test'
                                      junit 'build/test-results/test/*.xml'
                                      cucumber '**/reports/*.json'

                              }
                          }
                      }
stage("Code Analysis") {
    steps {
        script {
            withSonarQubeEnv(installationName: 'sonar', credentialsId: 'sonar-token') {
                bat "./gradlew sonarqube"
            }
        }
    }
}

stage("Quality Gate") {
    steps {
        script {
            timeout(time: 2, unit: 'MINUTES') {  // Add timeout
                waitForQualityGate abortPipeline: true
            }
        }
    }
}

        stage("Build") {
            steps {
                script {

                        bat './gradlew build'
                        bat './gradlew javadoc'
                        archiveArtifacts 'build/libs/*.jar'

                }
            }
        }
        stage("Deploy") {
            steps {
                script {

                        bat './gradlew publish'


                }
            }
        }
        stage("Mail Notification") {
            steps {
                script {

                        bat './gradlew sendMail'

                }
            }
        }
        stage("Slack Notification") {
                    steps {
                        script {

                                bat './gradlew slackNotification'

                        }
                    }
                }
    }

}