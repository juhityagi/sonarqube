pipeline {
  agent none
  stages {
    stage("build & SonarQube analysis") {
      agent any
      steps {
        sh 'echo "START ${env.STAGE_NAME}"'
        withSonarQubeEnv('SonarQube') {
          sh 'mvn clean package sonar:sonar'
        }
        sh 'echo "END ${env.STAGE_NAME}"'
      }
    }
    stage ("SonarQube analysis") { 
      steps { 
        catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
          script {
            def qualitygate = waitForQualityGate() 
            if (qualitygate.status != "OK") { 
              error "Pipeline aborted due to quality gate coverage failure: ${qualitygate.status}" 
            }
          }
        }
      } 
    }
    stage('Saving Logs') {
      agent any
      steps {
          sh 'printenv'
          sh 'echo "Saving logs to a new file in ${JENKINS_HOME}/LOGS folder..."'
          sh 'cat ${JENKINS_HOME}/jobs/SonarQubeDemo/branches/${GIT_BRANCH}/builds/${BUILD_NUMBER}/log >> ${BUILD_TAG}.txt'
      }
    }
    stage('Upload to AWS') {
      agent any
      steps {
        sh 'pwd'
        script {
          def date = new Date().format("yyyy-MM-dd", TimeZone.getTimeZone('UTC'))
          withAWS(region:'us-east-1',credentials:'aws-secrets') {
          sh 'echo "Uploading content with AWS creds"'
          s3Upload(pathStyleAccessEnabled: true, payloadSigningEnabled: true, file: "${env.BUILD_TAG}.txt" , bucket:'sksingh-jenkins-786', path: "SonarLogs/${date}/${env.BUILD_TAG}.txt")
        }
        }
      }
    }
  }
}
