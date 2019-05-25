# SOP6 Docker implementation
---

## :books: Libraries/tools
This project was started the goal of simulating a real software development workflow  
with CI (Continuous Integration) and CD (Continuous Deployment).

For this the following libraries/tools are used:

- Payara as application server (JavaEE)
- PostgreSQL as database
- Jenkins as automation server linked with this repo using a Webhook

Some more libraries/tools were necessary to get it all working as we have developed this project with Docker Desktop for   
Windows. They will be explained in detail in the next couple of paragraphs.

### :globe_with_meridians: Ngrok
Because this project is developed locally there was initially no URL for the Webhook to connect to.  
That's where Ngrok comes in. This tool exposes a specified port to the internet using a subdomain.  
Downloads for the different operating systems can be found on [ngrok.com/download](https://ngrok.com/download).

After extracting and running the script you can expose Jenkins to the GitHub Webhook like this:
```bash
   ngrok http 9090
```

### :floppy_disk: PostgreSQL shenanigans
Disclaimer: this issue is only present on Windows machines at the time of writing.  
When the PostgreSQL container would spin up there would be an error message stating:
```
FATAL: data directory "/var/lib/postgresql/data" has wrong ownership.  
The server must be started by the user that
owns the data directory.
```

As [this StackOverflow answer](https://github.com/docker/for-win/issues/445#issuecomment-405185621) shows there's a way 
around it. While this is not the best solution, we've got no choice judging by the comments 
(especially [this one](https://github.com/docker/for-win/issues/445#issuecomment-425650363)).

The fix above has been integrated into the ```docker-compose.yml``` file.  
:warning: **You still need to run ```docker volume create postgres_database``` before your first usage though!**  

Tip: in case you want to know where docker created the volume you can run ```docker volume inspect postgres_database```.

## :hammer_and_wrench: Configuration
Configuration steps can be found in ```jenkins-setup.md``` , ```payara-setup.md``` and ```sonarqube-setup.md``` because of the amount of 
screenshots.  
The PostgreSQL database doesn't need further configuration for this project.