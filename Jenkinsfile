pipeline {
  agent none
  stages {
    stage('Integration UI Test') {
      parallel {
        stage('Deploy') {
          agent any
          steps {
            sh './jenkins/scripts/deploy.sh'
            input message: 'Finished using the web site? (Click "Proceed" to continue)'
            sh './jenkins/scripts/kill.sh'
          }
        }
        stage('Headless Browser Test') {
          agent {
            docker {
              image 'maven:3-alpine'
              args '-v /root/.m2:/root/.m2'
            }
          }
          steps {
            script {
              echo 'Starting headless browser tests...'
              try {
                sh 'mvn -B -DskipTests clean package'
                sh 'mvn test'
              } catch (Exception e) {
                echo 'Caught exception: ' + e.toString()
                throw e
              } finally {
                echo 'Headless browser test stage complete'
              }
            }
          }
          post {
            always {
              junit 'target/surefire-reports/*.xml'
            }
          }
        }
      }
    }
  }
}
