pipeline {
  agent any
  options {
    buildDiscarder logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5')
  }
  stages {
    stage('Hello') {
      steps {
        sh '''
          java -version
        '''
      }
    }
    stage('cat README') {
      steps {
        sh '''
          cat README.md
        '''
      }
    }
    stage('say hello from dev'){
      steps {
 sh '''
         echo "hello from the aother side"
        '''
      }

    }
  }
}
