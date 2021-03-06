# Node Cookbook set up with Jenkins CI pipeline

## **Timings**

## **This lesson includes**

* Testing Cookbooks
* Configure Jenkins
* Cloud Drivers

---

## Testing Cookbook

Create a cookbook for Nginx and make sure that you have set up a GitHub repository for this cookbook.

Test it on your local machine using Chef's unit and integration tests

### Unit Test:
`chef rspec spec`
### Integration Test:
`kitchen test`

---

## Configure Jenkins

> Access Sparta's Jenkins server with `http://jenkins.spartaglobal.academy:8080`, log in with your credentials.

<p> Use the following steps to configure a Jenkins build to run a CI test for your Cookbook</p>

1. Create a freestyle job and give your project a name

2. Check GitHub Project, add your GitHub url for your cookbook (https version)

3. Check Git under Source Code Management, add your GitHub Git repository and select your credentials.
  * If you haven't set your credentials create them. You will need a new ssh private and public key so that Jenkins and GitHub can communicate

4. Specify the branch you would like to build
  * Note: Good practice is running your jenkins build from a dev branch, if it passes, merge your dev branch into master

5. Check 'Use secret text(s) or file(s)' under Build Environment, add AWS Access keys and secret

6. Check 'Inject environment variables to the build process'
  * Add `KITCHEN_YAML=.kitchen.cloud.yml` to Properties Content
  * Check 'Use Groovy Sandbox'

7. In the Build step select 'Execute Shell'
```
chef exec rspec spec
kitchen test
```

8. Under Post-build Action, select Git Publisher
  * Select 'Push Only if Build Succeeds'
  * Select 'Add Branch'
    * `Branch to push: master`
    * `Targer remote name: origin`

9. Switch to your GitHub repository
  * Under Settings > Webhooks > Add Webhook
  * Add `http://jenkins.spartaglobal.academy:8080/github-webhook/` to the Payload URL and save
> If it succeeds you should see a green tick and 'Payload delivered successfully'

---

### Running a Jenkins build when pushing new code

1. Check GitHub hook trigger for GITScm polling

2. Push some Code and check if the Jenkins Build runs, passes and pushed code onto master successfully.

---

### Running a Jenkins build from a pull request trigger

1. Check GitHub Pull Request Builder
  * Add GitHub API credentials, should be https://api.github.com : *Jenkins GitHub User*
  * Add Jenkins GitHub user to the admin list
  * Select Advance
    * Under trigger phrase, type a word which you will use to trigger the pull request
      * Remember your word, you will need it later
    * Select 'Only use trigger phrase for build triggering'
  * Remove information from Crontab line, we don't want the build to run periodically only when a pull request is initiated.


2. Add another secret text to Build Environment
  * Select the *Jenkins GitHub Users* GitHub Token
  * Add your own SSH User Private Key, select your credentials


3. Check SSH Agent in Bindings and add the *Jenkins GitHub Users* credentials


4. Switch to your GitHub repository
  * Protect your master branch, so that it requires you to have one approving review to merge a pull request into master
  * Add the *Jenkins GitHub User* as a reviewer


> Open a pull request into master and add a comment using your the word you set as your trigger phrase in your Jenkins build
