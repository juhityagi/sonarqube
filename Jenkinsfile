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
      def project="SonarQubeDemo"
      steps {
        sh 'printenv'
        sh 'echo ${project}'
        sh 'cat ${JENKINS_HOME}/jobs/${project}/branches/${GIT_BRANCH}/builds/${BUILD_NUMBER}/log >> ${BUILD_NUMBER}.log'
      }
    }
  }
}
