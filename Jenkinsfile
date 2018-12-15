pipeline {
    agent any
    stages {
        stage ('Checkout') {

        slackSend "Build Started - ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)"

        checkout scm

        stage 'Gradle Static Analysis'
        withSonarQubeEnv {
            sh "./gradlew clean sonarqube"
        }
    }
    post {
        slackSend "Build Finished - ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)"

        failure {
            slackSend "Build Failed - ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)"
        }
    }
} 
