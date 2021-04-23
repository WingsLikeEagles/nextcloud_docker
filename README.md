# nextcloud_docker
Dockerized NextCloud https://github.com/nextcloud/docker  
This repo is for setting up NextCloud in Docker containers using Portainer-CE. 

# Pre-requisites
You may want to follow my setup for Docker and Portainer with a local registry.  
- https://github.com/WingsLikeEagles/Docker_Portainer_setup  
It utilizes a local registry for version control and security.  
Pull the NextCloud image, tag it, and push it to your local registry
`docker pull nextcloud:21.0.0`
`docker tag nextcloud:21.0.0 localhost:5000/nextcloud:21.0.0`
`docker push localhost:5000/nextcloud:21.0.0`

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
  b. Modify the upload limit as needed
  c. Change the database name and user names as desired
5. Using the "Web Editor" Build method, paste in the modified docker-compose.yml
6. Click the `Deploy the Stack` button at the bottom.
