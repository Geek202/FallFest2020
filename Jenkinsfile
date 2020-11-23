pipeline {
  agent any
  environment {
    WEBHOOK_URL = credentials('discord-webhook')
    WEBHOOK_TITLE = "FallFest2020 Build #${BUILD_NUMBER}"
    JENKINS_HEAD = 'https://wiki.jenkins-ci.org/download/attachments/2916393/headshot.png'
  }
  
  stages {
    stage('Notify-Build-Start') {
      discordSend(
        title: "${WEBHOOK_TITLE} Started",
        successful: true,
        result: 'ABORTED',
        thumbnail: JENKINS_HEAD,
        webhookURL: WEBHOOK_URL
      )
    }
    
    stage('Build') {
      steps {
        sh '''chmod +x gradlew
./gradlew clean build'''
      }
    }

  }
  
  post {
    always {
      script {
        archiveArtifacts(artifacts: 'build/libs/*.jar', fingerprint: true, onlyIfSuccessful: true, allowEmptyArchive: true)
        
        if (env.CHANGE_ID == null) {
          discordSend(
            title: "${WEBHOOK_TITLE} Finished ${currentBuild.currentResult}",
            description: '```\n' + getChanges(currentBuild) + '\n```',
            successful: currentBuild.resultIsBetterOrEqualTo("SUCCESS"),
            result: currentBuild.currentResult,
            thumbnail: JENKINS_HEAD,
            webhookURL: WEBHOOK_URL
          )
        }
      }
    }
  }
}
