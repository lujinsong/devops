# Build Jenkins Docker Image. Run docker_in_docker.
## Docker hub login. In windows, just run docker login
```
docker login -u [yourdockerhubaccount] --password-stdin
```
## Build image with tag, don't forgot . as the current docker file folder.  
### `docker build -t <hub-user>/<repo-name>[:<tag>]`
```
example: docker build -t lujasper/devops:jenkins .  
```
## Push the image to your docker hub repository. You will need to create your repository first. 
### `docker push <hub-user>/<repo-name> .`
```
example: docker push lujasper/devops:jenkins 
```
## Run it with local data storage, which will persist even the container is purged. The folder /var/jenkins_home is the jenkins home of the jenkins container.
## For windows, check [Docker mounting in windows](https://rominirani.com/docker-on-windows-mounting-host-directories-d96f3f056a2c)
> To have docker run inside the docker container, need to add -v /var/run/docker.sock:/var/run/docker.sock
![Alt text](./images/dockersettings.jpg?raw=true "Docker Settings")
```
# windows env. 
mkdir c:/devops/jenkins
docker rm jenkins
docker run -d -v c:/devops/jenkins:/var/jenkins_home -v //var/run/docker.sock:/var/run/docker.sock -p 8080:8080 -p 50000:50000 --name jenkins  lujasper/devops:jenkins   
docker ps
## Use the container id above to connect to docker container.
docker exec -ti jenkins bash
```
```
# mac/linux env
mkdir /devops/jenkins
docker rm jenkins
docker run -d -v /devops/jenkins:/var/jenkins_home -v /var/run/docker.sock:/var/run/docker.sock -p 8080:8080 -p 50000:50000 --name jenkins  lujasper/devops:jenkins   
docker ps
## Use the container id above to connect to docker container.
docker exec -ti jenkins bash
```
## Check whether Jenkins is running via http://localhost:8080
## Please follow the instruction to create the admin user. When Jenkins is up first time, the user name is admin and password is in a secrete password file. Make sure to avoid the special characters in the password when you are prompted for new password. If java error occurs, this may be the cause of special character and try anothe password (delete the local storage volume for jenkins, for example every file and folder under c:/devops/jenkins). When the admin user is created, it will try to remove the original password file. So close the secrete password file before changing the password, otherwise Jenkins will fail to delete it. 
