node {
    // uncomment these 2 lines and edit the name 'node-4.4.7' according to what you choose in configuration
    // def nodeHome = tool name: 'node-4.4.7', type: 'jenkins.plugins.nodejs.tools.NodeJSInstallation'
    // env.PATH = "${nodeHome}/bin:${env.PATH}"

/*    stage('check tools') {
        sh "node -v"
        sh "npm -v"
        sh "bower -v"
        sh "gulp -v"
    }*/

    stage('checkout') {
        checkout scm
    }

    stage('clean') {
        sh "./mvnw clean"
    }

    stage('backend tests') {
        sh "./mvnw test -X -Xmx1024m -XX:MaxPermSize=256m"
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
}
post {
    slackSend(channel: "@veronica.digiorgio", message: "Thread reply #1")
    failure {
        slackSend(channel: "@veronica.digiorgio", message: "FAIL")
    }
}