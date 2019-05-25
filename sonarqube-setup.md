# :hammer_and_wrench: SonarQube setup

## 1. Set up SonarQube
In order to be able to write results to SonarQube we need to set up some things:

1. Open the [admin interface](http://localhost:9000) once the docker services are running

2. The default login information is:
    - username: admin
    - password: admin
    
    Use the 'log in' button to get to the dashboard with privileges.

3. Once you see the dashboard, create a project and give it an appropriate name (mine is 'SOP6').  
Remember this name as it's also the project key you'll use in Jenkins.

4. The prompt will guide you in creating an access token which will be used to for authorization from Jenkins. Give this token an appropriate name too (mine is 'Jenkins'). 

:warning: **important: make sure to copy the access token for use with Jenkins**

That's it! We're already done in SonarQube :thumbsup:

## 2. Set up Jenkins
Now it's time to link Jenkins to SonarQube so the result from the build step can be displayed in the SonarQube dashboard. In order to make this work you should follow these steps:

1. Install the 'SonarQube Scanner for Jenkins' by navigating to 'Manage Jenkins' -> 'Manage plugins' -> selecting the 'Available' tab and searching for 'SonarQube'.

2. Once installed, navigate back to 'Manage Jenkins' and select 'Configure System'.

3. Under 'SonarQube servers' leave the environment variables checkbox unchecked.  
Give the server a recognisable name (mine is 'SOP6 sonarqube') and fill in the Server URL http://host.docker.internal:9000. ```host.docker.internal``` will automatically resolve to your current IP address on Windows.  
Localhost can't be used here because we're inside the Docker container itself. This way port 9000 is not recognised on execution.  
The server authentication token is the token obtained from the steps mentioned above.   
Add a new credential with the default 'Global credentials' for Domain.  
Select 'Secret text' as 'Kind' and fill in 'Secret' and an appropriate 'Description' (mine is 'SOP6 SonarQube').  
Last but not least: save the new configuration

4. Navigate back to 'Manage Jenkins' and choose 'Global Tool Configuration'. Under 'SonarQube Scanner' you can select the binary that runs the analysis.  
Name the scanner appropriately (mine is 'SOP6 scanner') and let Jenkins automatically install the latest version from Maven.  
Again save the configuration.

5. The last step is to configure the Jenkins job itself.  
To do that select 'Configure' once you're inside the job interface.  
Scroll down to the 'Build' section and add a build step named 'Execute SonarQube Scanner'.  
Leave everything as is with the exception of 'Analysis properties'. In there you want to add the line ```sonar.projectKey={PROJECT_KEY_HERE}```.  
My line looks like this: ```sonar.projectKey=SOP6```.

Save everything and SonarQube should run once the job is triggered! :tada: