# Build Jenkins Docker Image
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
![Alt text](./images/dockersettings.jpg?raw=true "Docker Settings")
```
docker run -d -v c:/devops/jenkins:/var/jenkins_home -p 8080:8080 -p 50000:50000  lujasper/devops:jenkins   
docker ps
## Use the container id above to connect to docker container.
docker exec -ti 6bc1635bf907 bash
```
## Check whether Jenkins is running via http://localhost:8080
## Then following the instruction to set up and create admin user. Please avoid the special character in password. If java error occurs, this may be the cause and try anothe password. When the admin user is created, it will try to remove the original password file. Make sure it is closed, otherwise it will fail to delete it. 
