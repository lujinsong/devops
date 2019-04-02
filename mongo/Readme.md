
# Mongodb Docker Container
## Mongo docker official site https://hub.docker.com/_/mongo/
### If you need specific Mongodb version and add a monitoring service, you may use the docker file listed in the mongo docker official site above and build your own Dockerfile. The Dockerfile included is for Ubuntu environment. If your environment is windows, please use the windows image otherwise you will have to do some work around. Also use dos2unix.exe to convert any file you modified.

## Docker hub login. In windows, just run docker login
docker login -u [yourdockerhubaccount] --password-stdin
## Build image with tag, don't forgot . as the current docker file folder. You run the command where the Dockerfile is.
### `docker rmi -f <hub-user>/<repo-name>[:<tag>]`
### `docker build -t <hub-user>/<repo-name>[:<tag>] --build-arg MONGO_INITDB_ROOT_USERNAME=root --build-arg MONGO_INITDB_ROOT_PASSWORD=example .`
example: docker build -t lujasper/devops:mongo --build-arg MONGO_INITDB_ROOT_USERNAME=root --build-arg MONGO_INITDB_ROOT_PASSWORD=example .  
## Push the image to your docker hub repository. You will need to create your repository first. 
### `docker push <hub-user>/<repo-name>`
example: docker push lujasper/devops:mongo  

## Run Mongo with local data storage, which will persist even the container is purged. The folders /var/data and /var/configdb need to be mapped to your local folders that must be created before you run the command, for example c:\devops\mongo\data and c:\devops\mongo\configdb.
## For windows, check [Docker mounting in windows](https://rominirani.com/docker-on-windows-mounting-host-directories-d96f3f056a2c)
![Alt text](./images/dockersettings.jpg?raw=true "Docker Settings")

docker run -d --rm -v c:/devops/mongo/data/db:/data/db -v c:/devops/mongo/data/configdb:/data/configdb -p 27017:27017  lujasper/devops:mongo 
### above command may not work in windows environment due to imcompatible file mode, use docker volumn to fix it. The drawback is that data is stored inside Docker not local driver. Please find the section at the end of this document on how to extract the volume.
### you may need to remove existing one before create a new volumn. docker system prune. docker volume rm -f --name=mongodata. 
docker volume create --name=mongodata
docker run -d --rm -v mongodata:/data/db -p 27017:27017  lujasper/devops:mongo 
## docker start|stop lujasper/devops:mongo to start or stop mongodb server

docker ps
## Use the container id above to connect to docker container.
docker exec -ti 6bc1635bf907 bash

## Download Mongodb Compass to test it
mongo localhost/mydb

# Security
## Enable security via mongodb.conf file. Let's add it to the image(path: /etc/mongo/mongod.conf) in the Dockerfile. 
security:
   authorization: enabled
## Using docker volume
docker volume create --name=mongodata
### Run without config file first and set up admin account first
> docker run -v mongodata:/data/db -p 27017:27017 --name mymongodb  -ti --rm lujasper/devops:mongo   
> docker ps #open another termina and check whether mymongodb is running or not.   
> docker exec -ti mymongodb bash   
>>  root@aa01ffdb6f25:/# mongo mongodb://localhost:27017   
>>>   \>use admin   
      \>db.createUser(
      {
        user: "admin",
        pwd: "admin123",
        roles: [ { role: "userAdminAnyDatabase", db: "admin" } ]
      }
    )

### Exit the docker container, ctrl+c or docker stop mymongodb. Then run with the config file.
> docker run -v mongodata:/data/db -p 27017:27017 -ti --rm --name mymongodb lujasper/devops:mongo --config /etc/mongo/mongod.conf 




# Backup Docker Volume, Save it a container and then extract the whole container.
Sharing Directories using Volumes
In the "Backup, restore, or migrate data volumes" section you have:
BACKUP:
sudo docker run --rm --volumes-from DATA -v $(pwd):/backup busybox tar cvf /backup/backup.tar /data
--rm: remove the container when it exits
--volumes-from DATA: attach to the volumes shared by the DATA container
-v $(pwd):/backup: bind mount the current directory into the container; to write the tar file to
busybox: a small simpler image - good for quick maintenance
tar cvf /backup/backup.tar /data: creates an uncompressed tar file of all the files in the /data directory
RESTORE:

### create a new data container
$ sudo docker create -v /data --name DATA2 busybox true
### untar the backup files into the new container᾿s data volume
$ sudo docker run --rm --volumes-from DATA2 -v $(pwd):/backup busybox tar xvf /backup/backup.tar
data/
data/sven.txt
### compare to the original container
$ sudo docker run --rm --volumes-from DATA -v `pwd`:/backup busybox ls /data
sven.txt