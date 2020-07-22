pipeline {
  def project = "${env.JOB_NAME}".split('/')[0]
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
        sh 'cat ${JENKINS_HOME}/jobs/${jobBaseName}/branches/${GIT_BRANCH}/builds/${BUILD_NUMBER}/log >> ${BUILD_NUMBER}.log'
      }
    }
  }
}
