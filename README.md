# multibranch-sample-app

![Image description](https://github.com/nashpaz123/multibranch-sample-app/blob/main/1.png)

![Image description](https://github.com/nashpaz123/multibranch-sample-app/blob/main/2.png)

Let’s create a Jenkinsfile in the root directory, and for now let’s keep it simple, like this:

```
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
```

After you commit and push the Jenkinsfile to the remote repository, you should be able to see the file created in the main branch.

Now, we need to ask Jenkins to scan the repository to find the new branch we just created. Head over to your Jenkins job, and click on “Scan Multiple Pipeline Now” on the left side of the screen. Wait for a minute and refresh the screen. Now you should see that a new branch appears and a Jenkins job has been created automatically.


![Image description](https://github.com/nashpaz123/multibranch-sample-app/blob/main/3.png)

You can inspect the job and see the logs to confirm that it worked.

# Adding More Branches to the Repository
At this point, you haven’t seen the benefit of creating a multibranch pipeline job. So, let’s create two more branches in git to see what happens in Jenkins. Open a terminal in your computer and clone the git repository you created before (in case you haven’t done so). Then, run the following commands to create two branches from the main branch:

```
git checkout -b fix-123

git checkout main

git checkout -b dev-456
```

Now you need to push these branches to the remote repository so that the Jenkins job is able to see them and create the jobs automatically. To do so, run these commands:

```
git push --set-upstream origin dev-456

git push --set-upstream origin fix-123
```

If you go to GitHub, you should see that the two new branches were pushed successfully.

Let’s go back to your multibranch job in Jenkins. Click again on the “Scan Multibranch Pipeline Now” to discover the new branches and create the new jobs. Wait for a minute and refresh the page. You should see that two new jobs were created because of the new branches you just pushed.


![Image description](https://github.com/nashpaz123/multibranch-sample-app/blob/main/4.png)

Those two new jobs have the same configuration as the one you created initially (the multibranch pipeline). Therefore, you can’t edit those jobs, at least not from the UI, but you can run them and view its logs and change the pipeline through the Jenkinsfile. This way, you can promote changes to other branches and test the pipeline several times before you push it to the main branch. Let’s see how this would work by making a change in the job through git branches.

![Image description](https://github.com/nashpaz123/multibranch-sample-app/blob/main/5.png)
