Before you begin:

Create a new free github.com account and Create a repository named: multibranch-sample-app, 
![Image description](https://github.com/nashpaz123/multibranch-sample-app/blob/main/7.png)

clone it to your linux machine:
```
git clone https://github.com/MY_COOL_ACCOUNT_NAME/multibranch-sample-app.git
cd multibranch-sample-app
```
Create a personal access token (it is the git push password): Under https://github.com/settings/tokens/new -> choose a name and mark all permissions. save the token somewhere safe. https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token )

# multibranch-sample-app

![Image description](https://github.com/nashpaz123/multibranch-sample-app/blob/main/1.png)

![Image description](https://github.com/nashpaz123/multibranch-sample-app/blob/main/2.png)

Let’s create a file called: **Jenkinsfile** in the root directory. you can use vim or create it in github.com by clicking create new file. make sure to git pull or git clone again:
![Image description](https://github.com/nashpaz123/multibranch-sample-app/blob/main/8.png)

for now let’s keep it simple, like this:

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

After you commit and push the Jenkinsfile from the local machine to the remote repository:
```
git push
```

(or `git pull` from the remote if you created the file in the github.com console), you should be able to see the file created in the main branch.

Now, we need to ask Jenkins to scan the repository to find the new branch we just created. Head over to your Jenkins job, and click on “Scan Multiple Pipeline Now” on the left side of the screen. Wait for a minute and refresh the screen. Now you should see that a new branch appears and a Jenkins job has been created automatically.


![Image description](https://github.com/nashpaz123/multibranch-sample-app/blob/main/3.png)

You can inspect the job and see the logs to confirm that it worked.

# Adding More Branches to the Repository
At this point, you haven’t seen the benefit of creating a multibranch pipeline job. So, let’s create two more branches in git to see what happens in Jenkins. Open a terminal in your computer and clone the git repository you created before (in case you haven’t done so). Then, run the following commands to create two branches from the main branch:

```
git pull #to be sure that were synced with the remote.

git checkout -b fix-123

git checkout main

git checkout -b dev-456
```

Now you need to push these branches to the remote repository so that the Jenkins job is able to see them and create the jobs automatically. To do so, run these commands:

```
git push --set-upstream origin dev-456

git push --set-upstream origin fix-123
```

## Important: 
#### (You'll be asked for user and password, the user is the account name, 
#### Your password for pushing is NOT the account password. It needs to be the personal access token under https://github.com/settings/tokens/new -> choose a name and mark all permissions. save the token somewhere safe. https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token )

If you go to GitHub now, you should see that the two new branches were pushed successfully.

Let’s go back to your multibranch job in Jenkins. Click again on the “Scan Multibranch Pipeline Now” to discover the new branches and create the new jobs. Wait for a minute and refresh the page. You should see that two new jobs were created because of the new branches you just pushed.


![Image description](https://github.com/nashpaz123/multibranch-sample-app/blob/main/4.png)

Those two new jobs have the same configuration as the one you created initially (the multibranch pipeline). Therefore, you can’t edit those jobs, at least not from the UI, but you can run them and view its logs and change the pipeline through the Jenkinsfile. This way, you can promote changes to other branches and test the pipeline several times before you push it to the main branch. Let’s see how this would work by making a change in the job through git branches.

![Image description](https://github.com/nashpaz123/multibranch-sample-app/blob/main/5.png)

Making Changes Through Git
Head over to the fix-123 branch to make a small change. Run the following command:

`git checkout fix-123`

Let's create a new README.md file with the following content (replace the old README.md):

#multibranch-sample-app

Then, modify the Jenkinsfile to print out the content of the README.md file, but only for those branches whose name starts with “fix-”, like this:

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

    stage('cat README') {
      when {
        branch "fix-*"
      }
      steps {
        sh '''
          cat README.md
        '''
      }
    }

  }
}
```

Now add, commit, and push those changes to the remote repository, like this:

```
git add .

git commit -m "Adding the README.md file"

git push
```

Head back over to your Jenkins instance and open the fix-123 job, then run the job. You should see the new changes and the log of the job, like this:

Checking out Revision 69c8f75ccf0a106a152bdb698ffc0938344e6e54 (fix-123)

 > git config core.sparsecheckout # timeout=10

 > git checkout -f 69c8f75ccf0a106a152bdb698ffc0938344e6e54 # timeout=10

Commit message: "Update Jenkinsfile"

 > git rev-list --no-walk fa6bd0e14e37d810dc16f73a4578d2406932a3b0 # timeout=10
 

[Pipeline] }

[Pipeline] // stage

[Pipeline] withEnv

[Pipeline] {

[Pipeline] stage

[Pipeline] { (Hello)

[Pipeline] sh

+ java -version

openjdk version "1.8.0_302"

OpenJDK Runtime Environment (build 1.8.0_302-b08)

OpenJDK 64-Bit Server VM (build 25.302-b08, mixed mode)

[Pipeline] }

[Pipeline] // stage

[Pipeline] stage

[Pipeline] { (cat README)

[Pipeline] sh

+ cat README.md

#multibranch-sample-app

[Pipeline] }

[Pipeline] // stage

[Pipeline] }

[Pipeline] // withEnv

[Pipeline] }

[Pipeline] // node

[Pipeline] End of Pipeline

Finished: SUCCESS

---

As you can see, the content of the README.md is there because the job got the update we did on the Jenkinsfile. If you run the main job again, you won't see the new change. But to confirm you can have custom behaviors in your pipelines, let's merge the fix-123 branch to main. The log should be the same as before because the logic for printing out the content of the README.md file is only for the “fix-” branches. Let's run the following commands:

```
git checkout main

git merge fix-123

git push
```

Let's also remove the local and remote fix-123 branch:

```
git branch -d fix-123

git push origin --delete fix-123
```

Head over to your Jenkins instance and run a scan again. You should see that the fix-123 job disappears when you refresh the page.

![Image description](https://github.com/nashpaz123/multibranch-sample-app/blob/main/6.png)

Additionally, when you run the main job, you shouldn´t see the contents of the README.md file in the logs and message saying that the stage has been skipped, like this:

Checking out Revision 346a949925b2dd5b130de8c2a39a3efe8ccd6a96 (main)

 > git config core.sparsecheckout # timeout=10

 > git checkout -f 346a949925b2dd5b130de8c2a39a3efe8ccd6a96 # timeout=10

Commit message: "Merge pull request #1 from nashpaz123/fix-123"

 > git rev-list --no-walk fa6bd0e14e37d810dc16f73a4578d2406932a3b0 # timeout=10

[Pipeline] }

[Pipeline] // stage

[Pipeline] withEnv

[Pipeline] {

[Pipeline] stage

[Pipeline] { (Hello)

[Pipeline] sh

+ java -version

openjdk version "1.8.0_302"

OpenJDK Runtime Environment (build 1.8.0_302-b08)

OpenJDK 64-Bit Server VM (build 25.302-b08, mixed mode)

[Pipeline] }

[Pipeline] // stage

[Pipeline] stage

[Pipeline] { (cat README)

Stage "cat README" skipped due to when conditional

[Pipeline] }

[Pipeline] // stage

[Pipeline] }

[Pipeline] // withEnv

[Pipeline] }

[Pipeline] // node

[Pipeline] End of Pipeline

Finished: SUCCESS

# Conclusion
Working with multiple branches at the same time isn’t a problem in Jenkins. You saw how easy it is to create a multibranch pipeline job where Jenkins creates a new independent job automatically for every branch you create. You don't have to worry about doing maintenance as Jenkins will remove the job as well when the branch is removed. And if you need to have custom stages for branches different than the one you use for production, you can do that, too, by defining the custom behavior in the Jenkinsfile.


