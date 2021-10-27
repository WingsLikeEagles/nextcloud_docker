# nextcloud_docker
Dockerized NextCloud https://github.com/nextcloud/docker  
This repo is for setting up NextCloud in Docker containers using Portainer-CE.  
At this time this example is just using HTTP (no S).  So don't use it over the Internet because your password will be exposed as well as your files and data.  You may want to add on <a href="https://letsencrypt.org/how-it-works/">Let's Encrypt</a> or another SSL certificate provider.  And make the necessary changes to the HAProxy services.

# Pre-requisites
You may want to follow my setup for Docker and Portainer with a local registry.  
- https://github.com/WingsLikeEagles/Docker_Portainer_setup  
It utilizes a local registry for version control and security.  
Pull the NextCloud image, tag it, and push it to your local registry  
`docker pull nextcloud:21.0.0`  
`docker tag nextcloud:21.0.0 localhost:5000/nextcloud:21.0.0`  
`docker push localhost:5000/nextcloud:21.0.0`  

# HAProxy
At this time you'll need to manually create the haproxy.cfg file and put it on the nextcloud_haproxy volume.  You'll need to change the line to reflect your domain replacing the `-i localhost` with `-i yourdomain.com`.  
Also required is the `errors` folder which contains the 400's and 500's error messages.  This can be pulled from another running haproxy container.   
Alternatively you can make your own image and copy in the cfg file.  This seems undesirable to me.  
`docker pull haproxy:2.3`  
`docker tag haproxy:2.3 localhost:5000/haproxy:2.3`  
`docker push localhost:5000/haproxy:2.3`

## Create a SSL Key for secure access using HTTPS protocol


# Create new Stack in Portainer
After you have Portainer setup and configured how you want it:  
1. Create a new Stack  
2. Name the stack (does not really matter too much, but it will be used as a pre-fix for the name of your containers)  
3. Edit the docker-compose.yml to suite your needs  
  a. Change the passwords for the following  
    i. db: MYSQL_ROOT_PASSWORD  
    ii. db: MYSQL_PASSWORD  
    iii. app: MYSQL_PASSWORD  
    iv. app: NEXTCLOUD_ADMIN_PASSWORD  
  b. Modify the upload limit as needed (2G is probably fine for must use cases, I have 12G because I need to upload large ISO files)  
  c. Change the database name and user names as desired  
5. Using the "Web Editor" Build method, paste in the modified docker-compose.yml  
6. Click the `Deploy the Stack` button at the bottom.  

# Add host and protocol overwites to NextCloud config.php file
You will need to adjust the configuration of NextCloud to ensure HTTPS is used.  The following two lines need to be added to the CONFIG array  
```
  'overwrite.cli.url' => 'https://files.roysdontech.com',
  'overwriteprotocol' => 'https',
```
