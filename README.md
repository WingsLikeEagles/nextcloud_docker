# nextcloud_docker
Dockerized NextCloud https://github.com/nextcloud/docker  
This repo is for setting up NextCloud in Docker containers using Portainer-CE.  
This example includes instructions for making your own SSL certificate and key.  This is secure if you keep the key secure, and pay attention to the fingerprint.  You may want to add on <a href="https://letsencrypt.org/how-it-works/">Let's Encrypt</a> or another SSL certificate provider.  And make the necessary changes to the HAProxy services.  

# Pre-requisites
You may want to follow my setup for Docker and Portainer with a local registry.  
- https://github.com/WingsLikeEagles/Docker_Portainer_setup  
It utilizes a local registry for version control and security.  
Pull the NextCloud image, tag it, and push it to your local registry  
`docker pull nextcloud:27.1.3-apache`  
`docker tag nextcloud:27.1.3-apache localhost:5000/nextcloud:27.1.3-apache`  
`docker push localhost:5000/nextcloud:27.1.3-apache`  

# HAProxy
At this time you'll need to manually create the haproxy.cfg file and put it on the nextcloud_haproxy volume.  You'll need to change the line to reflect your domain replacing the `-i localhost` with `-i yourdomain.com`.  
Also required is the `errors` folder which contains the 400's and 500's error messages.  This can be pulled from another running haproxy container.   
Alternatively you can make your own image and copy in the cfg file.  This seems undesirable to me.  
`docker pull haproxy:2.8.4`  
`docker tag haproxy:2.8.4 localhost:5000/haproxy:2.8.4`  
`docker push localhost:5000/haproxy:2.8.4`

## Create a SSL Key for secure access using HTTPS protocol
On a Linux host with OpenSSL installed you may have luck with this command to make a self-signed cert.  
`openssl req -x509 -nodes -newkey rsa:2048 -keyout ./files.mydomain.com.pem -out ./files.mydomain.com.pem -days 365`  
Copy this file, files.mydomain.com.pem, into the Docker volume for the HAProxy container.  
This may be something like, `/var/lib/docker/volumes/mydomain_nextcloud_config/_data`  

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

# Add host and protocol overwites to NextCloud config
Option 1: Use the proper environment variables in your Docker Compose file.  See the example in this repo where these  lines are added to the docker-compose.yml file:  
```
      - NEXTCLOUD_TRUSTED_DOMAINS=files.mydomain.com  
      - OVERWRITEHOST=files.mydomain.com  
      - OVERWRITEPROTOCOL=https  
```

Option 2: Manually adjust the configuration of NextCloud in the config.php file to ensure HTTPS is used.  The following lines need to be added to the CONFIG array:  
```
  'trusted_domains' =>
  array (
    0 => 'files.roysdontech.com',
  ),
  'overwrite.cli.url' => 'https://files.mydomain.com',
  'overwriteprotocol' => 'https',
```
