# :hammer_and_wrench: Jenkins setup

## 1. Set up GitHub connection + Webhook
In order to start the Jenkins flow after committing we need to link it with a GitHub Webhook.  
Follow these steps:

1. Generate a GitHub Personal Access Token:  
    1.01 Click on your GitHub avatar  
    1.02 Select Settings  
    1.03 Select 'Developer settings'  
    1.04 Select 'Personal access tokens'  
    1.05 Click generate new token
    1.06 Enter your GitHub password  
    1.07 Enter a meaningful name (mine is SOP6-Jenkins)  
    1.08 Select permissions admin:repo_hook:read, repo:status and repo:public_repo  
    1.09 Generate token  
    1.10 Copy the token.  
    :warning: Be careful, this is your only chance to copy it!  

2. Add Access Token to Jenkins  
    2.1 Select 'Credentials' in the left column of the dashboard  
    2.2 Click on 'Global' under domain  
    2.3 Select 'Add Credentials'  
    2.4 Under 'Kind' select 'Secret text'  
    2.5 Keep 'Scope' at 'Global'  
    2.6 Paste the GitHub Acccess Token in 'Secret'  
    2.7 Enter a meaningful description (mine is 'SOP6 GitHub secret')  
    
3. Set up Jenkins GitHub settings  
    3.1 Select 'Manage Jenkins' in the left column of the dashboard  
    3.2 Select 'Configure System'  
    3.3 Under 'GitHub' add a GitHub server with the default API URL  
    3.4 Select the recently created credentials ('SOP6 GitHub secret')  
    3.5 Apply the changes and test the connection using the 'Test Connection' button  

    :warning: **Note:** You have 5000 requests per hour for all GitHub integrations (not just Jenkins)

4. Change GitHub repository settings  
    4.1 Click 'Settings'  
    4.2 Select 'Webhooks'  
    4.3 Select 'Add webhook'  
    4.4 Fill the 'Payload URL' input with ```{YOUR_JENKINS_URL}/github-webhook/```.  
    In our case that'll be the Ngrok URL shown in the console as we're exposing our localhost  
    4.5 Select 'application/json' as Content type  
    4.6 Leave 'Secret' blank and select 'Just the push event' as trigger  
    4.7 Make sure the 'Active' checkbox is ticked  
    4.8 Add the webhook  

5. Set up the Jenkins job itself  
   5.1 Select the desired job (mine is SOP6)  
   5.2 Select 'Settings'  
   5.3 Under 'Build Triggers' check 'GitHub hook trigger for GITScm polling'  
   5.4 Make sure to save the configuration  
   
DONE! :smile:
Jenkins should now start your job whenever a new commit is pushed to your repository.

[Source](https://blog.tentamen.eu/jenkins-and-github-integration-using-webhooks/)

## 2. Configure Maven in Jenkins
In order to run Maven commands we need to have Maven itself installed (duh :man_shrugging:).  
There are 2 ways of doing that:

1. Have Maven installed locally
2. Instruct Jenkins to download Maven itself

As we want to depend on containerization instead of local configurations we're going for option 2.

To do this you need to follow these steps:  
1. Select 'Manage Jenkins' in the left pane of the dashboard  
2. Select 'Global Tool Configuration'
3. Under 'Maven' select 'Maven installations...' followed by 'Add Maven'
4. Enter a meaningful name (mine is 'SOP6 Maven version')
5. Select 'Install automatically'
6. Use the latest version of 'Install from Apache'
7. Make sure to save everything

Now you should be able to run Maven commands in your Jenkins job! :tada:

## 3. Run mvn clean and mvn package
Running these commands will delete any previous build artifacts, compile the code, run the unit tests and generate a 
JAR file.

This is how to set it up:

1. Select the desired job (mine is SOP6)
2. Select 'Settings'
3. Under 'Build' select 'Add build step'
4. Select 'Invoke top-level Maven targets'
5. Select the Maven just configured Maven version (SOP6 Maven version)
6. Add the 'clean package' text in the 'Goals' field

By default, the build job will fail if the code does not compile or if any of the unit tests fail.

## 4. Send email when build fails
TODO