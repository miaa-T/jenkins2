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
        stage('Send Email') {
                   steps {
                       script {
                           emailext (
                               subject: "Jenkins Build ${currentBuild.currentResult}: Job ${env.JOB_NAME}",
                               body: """
                                   Build Status: ${currentBuild.currentResult}
                                   Build Number: ${env.BUILD_NUMBER}
                                   Build URL: ${env.BUILD_URL}
                                   """,
                               to: 'mahdia.toubal@gmail.com',
                               from: 'mahdia.b964@gmail.com',
                           )
                       }
                   }
               }
         /* stage('Notify Slack') {
                    steps {
                        slackSend (
                            color: '#36a64f', // Green color for success
                            message: "Build completed for ${env.JOB_NAME} - Version ${env.CHANGE_NUMBER}",
                            webhook: "${env.SLACK_WEBHOOK_URL}"
                        )
                    }
                    post {
                        always {
                            echo 'Slack notification sent'
                        }
                    }
                }*/

}}