node {
    stage 'Checkout'

    checkout scm

    stage 'Gradle Static Analysis'
    withSonarQubeEnv {
        sh "./mvnw clean sonar:sonar"
    }
}

stage("Quality Gate"){
  sleep(10);
  timeout(time: 1, unit: 'MINUTES') { // Just in case something goes wrong, pipeline will be killed after a timeout
    def qg = waitForQualityGate() // Reuse taskId previously collected by withSonarQubeEnv
    if (qg.status != 'OK') {
      error "Pipeline aborted due to quality gate failure: ${qg.status}"
    }
  }
}
post {
	slackSend(channel: "UELV42KJ8", message: "Thread reply #1")
}