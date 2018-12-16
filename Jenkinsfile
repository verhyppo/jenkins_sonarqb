node {
    // uncomment these 2 lines and edit the name 'node-4.4.7' according to what you choose in configuration
    // def nodeHome = tool name: 'node-4.4.7', type: 'jenkins.plugins.nodejs.tools.NodeJSInstallation'
    // env.PATH = "${nodeHome}/bin:${env.PATH}"
    try {
        stage('checkout') {
            checkout scm
        }

        stage('clean package') {
            sh "./mvnw package -DskipTests"
        }

        stage('SONAR static analysis') {
            withSonarQubeEnv {
                sh "./mvnw clean sonar:sonar"
            }
        }

        stage("SONAR quality gate") {
          sleep(10);
          timeout(time: 1, unit: 'MINUTES') { // Just in case something goes wrong, pipeline will be killed after a timeout
            def qg = waitForQualityGate() // Reuse taskId previously collected by withSonarQubeEnv
            if (qg.status != 'OK') {
              error "Pipeline aborted due to quality gate failure: ${qg.status}"
            }
          }
        }

        stage('packaging') {
            sh "./mvnw package -Pprod -DskipTests"
        }
    } finally {
        slackSend(channel: "@veronica.digiorgio", message: "build Finished")
    }
}