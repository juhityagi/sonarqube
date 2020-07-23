pipeline {
  agent none
  stages {
    stage("build & SonarQube analysis") {
      agent any
      steps {
        withSonarQubeEnv('SonarQube') {
          sh 'mvn clean package sonar:sonar'
        }
      }
    }
    stage("Quality Gate") {
      steps {
        timeout(time: 1, unit: 'HOURS') {
          waitForQualityGate abortPipeline: true
        }
      }
    }
    stage('Print ENV') {
      agent any
      steps {
        sh 'echo "Saving logs to a new file in ${JENKINS_HOME}/LOGS folder..."'
        sh 'cat ${JENKINS_HOME}/jobs/SonarQubeDemo/branches/${GIT_BRANCH}/builds/${BUILD_NUMBER}/log >> ${BUILD_TAG}.log'
      }
    }
    stage('Upload to AWS') {
      agent any
      steps {
          sh 'pwd'
          withAWS(region:'us-east-1',credentials:'aws-secrets') {
            sh 'echo "Uploading content with AWS creds"'
            s3Upload(pathStyleAccessEnabled: true, payloadSigningEnabled: true, file: "${env.BUILD_TAG}.log" , bucket:'sksingh-jenkins-786')
          }
      }
    }
  }
}
