pipeline {

  agent any
  tools {
               maven 'Maven 3.8.5'  // Must match the name exactly as configured
           }
   environment {
          deployStatus = ''
           MAVEN_REPO_USERNAME = credentials('f57065b6-df32-4c92-a41d-8423325acaac')
                  MAVEN_REPO_PASSWORD = credentials('f57065b6-df32-4c92-a41d-8423325acaac')


      }


    stage('Publish') {
                steps {
                    withGradle {
                        script {
                            withEnv(["MAVEN_REPO_USERNAME=${env.MAVEN_REPO_USERNAME}", "MAVEN_REPO_PASSWORD=${env.MAVEN_REPO_PASSWORD}"]) {
                                bat 'gradlew publish'
                            }
                        }
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
        stage('Publish') {
               steps {
                   script {

                        withMaven(
                                              maven: 'Maven',
                                               globalMavenSettingsConfig: 'MyGlobalSettings'
                                           )
                           {
                           bat 'mvn deploy:deploy-file ' +
                               '-DgroupId=com.example ' +
                               '-Dversion=1.0-SNAPSHOT ' +
                               '-Dpackaging=jar ' +
                               '-DrepositoryId=maven-repo ' +
                               '-Durl=https://mymavenrepo.com/repo/rJIKOUgtFy4prBAMsqKs'
                       }
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
          /*stage('Slack Notification') {
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