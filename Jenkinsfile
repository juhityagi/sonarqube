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
        sh 'printenv'
        sh 'cat ${JENKINS_HOME}/jobs/SonarQubeDemo/branches/${GIT_BRANCH}/builds/${BUILD_NUMBER}/log | tee test.log'
      }
    }
  }
}
