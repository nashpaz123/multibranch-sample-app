# multibranch-sample-app

![Image description](https://github.com/nashpaz123/multibranch-sample-app/blob/main/1.png)

![Image description](https://github.com/nashpaz123/multibranch-sample-app/blob/main/2.png)

Let’s create a Jenkinsfile in the root directory, and for now let’s keep it simple, like this:

`
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

  }
}
`

After you commit and push the Jenkinsfile to the remote repository, you should be able to see the file created in the main branch.

Now, we need to ask Jenkins to scan the repository to find the new branch we just created. Head over to your Jenkins job, and click on “Scan Multiple Pipeline Now” on the left side of the screen. Wait for a minute and refresh the screen. Now you should see that a new branch appears and a Jenkins job has been created automatically.


![Image description](https://github.com/nashpaz123/multibranch-sample-app/blob/main/3.png)

You can inspect the job and see the logs to confirm that it worked.



![Image description](https://github.com/nashpaz123/multibranch-sample-app/blob/main/4.png)

![Image description](https://github.com/nashpaz123/multibranch-sample-app/blob/main/5.png)
