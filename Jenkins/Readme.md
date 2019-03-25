# Build Jenkins Docker Image
# docker login first, in windows environment, just run docker login
docker login -u [yourgithubaccount] --password-stdin
# build image with tag, don't forgot . as the current docker file folder.
# docker build -t <hub-user>/<repo-name>[:<tag>]
docker build -t lujasper/devops:jenkins .  
# push it to your docker hub repository. You will need to create your repository first. 
# docker push <hub-user>/<repo-name>
docker push lujasper/devops:jenkins #

# Run it with local data storage /var/jenkins_home is the jenkins home of the jenkins container.
# for windows, check this https://rominirani.com/docker-on-windows-mounting-host-directories-d96f3f056a2c
![Alt text](./images/dockersettings.jpg?raw=true "Docker Settings")

docker run -d -v c:/devops/jenkins:/var/jenkins_home -p 8080:8080 -p 50000:50000  lujasper/devops:jenkins
docker ps
# use the container id above
docker exec -ti 6bc1635bf907 bash

# check whether Jenkins is running. http://localhost:8080
# follow the instruction to set up and create admin user. Please avoid the special character in password. If you saw the java error, this may be the problem and give anothe password. When the admin user is created, it will try to remove the original password file. Make sure it is closed, otherwise it will fail to delete it. 
