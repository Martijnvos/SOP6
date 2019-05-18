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
In order to send out mail you need to have a SMTP server that can handle it.  
I tried to hook it up to the Fontys organizational Outlook account I have, but that didn't go according to plan because 
a lack of direct send capabilities ([Source](https://answers.microsoft.com/en-us/msoffice/forum/all/client-was-not-authenticated-to-send-anonymous/d405bcb0-f40c-42fa-b1b2-477597100123)). 
I settled on using [SendGrid](https://sendgrid.com) as the sender of the email.

This is how I set it all up:

1. Select 'Manage Jenkins' in the left pane and navigate to 'Configure System'
2. Scroll down to 'Jenkins Location' and fill in 'System Admin e-mail address'
3. Scroll down to 'E-mail notification'
4. Fill your SMTP details using SMPT authentication. For me:  
    * SMTP Server: smtp.sendgrid.net
    * User Name: apikey
    * Password: {YOUR_SENDGRID_API_KEY}
    * Keep 'Use SSL' **off**!
    * SMTP Port: 587
    * Reply-To Address: {YOUR_MAIL_ADDRESS}
    * Keep 'Charset' at UTF-8
    * Save the changes

Helpful sources for this were [this Jenkins issue](https://issues.jenkins-ci.org/browse/JENKINS-47939?focusedCommentId=319577&page=com.atlassian.jira.plugin.system.issuetabpanels:comment-tabpanel#comment-319577)
in combination with [this guide](https://www.learnitguide.net/2018/08/how-to-configure-jenkins-mail.html).  
Some of the information in these articles is contradicting though.  
The combination of them made it work, so don't blindly follow the links.

In order to actually fire the e-mail on build failure you have to do the following:

1. Select the job (mine is SOP6)
2. Select 'Configure' in the left pane
3. Scroll down to 'Post-build actions' and select 'Add post-build action'
4. Select 'Email Notification' from the dropdown
5. Fill in the recipient(s) mail address(es)
6. Make sure 'Send e-mail for every unstable build' is checked.  
Sending separate emails to individuals who broke the build is optional here.