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

    stage('development tests') {
      when {
        branch "dev-*"
      }
      steps {
        sh '''
          echo "ase li yeled"
          echo "alls developed and tested now"
        '''
      }
    }
    stage('cat README') {
      when {
        branch "fix-*"
      }
      steps {
        sh '''
          cat README.md
          echo "alls fixed now"
        '''
      }
    }
    stage('cat README') {
      when {
        branch "main"
      }
      steps {
        sh '''
          echo "logic for releaseing to docker hub to aws to 1000000 dollar
        '''
      }
    }
  }
}
