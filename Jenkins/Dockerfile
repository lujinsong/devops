FROM jenkins/jenkins:lts
RUN /usr/local/bin/install-plugins.sh docker-slaves github-branch-source:1.8 cloudfoundry google-deployment-manager google-login google-cloudbuild docker-swarm aws-java-sdk docker-java-api
USER root
RUN apt-get update -qq \
    && apt-get install -qqy apt-transport-https ca-certificates curl gnupg2 software-properties-common 
RUN curl -fsSL https://download.docker.com/linux/debian/gpg | apt-key add -
RUN add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/debian \
   $(lsb_release -cs) \
   stable"
RUN apt-get update  -qq \
    && apt-get install docker-ce=17.12.1~ce-0~debian -y
RUN usermod -aG docker jenkins
RUN chmod 777 /var/run/docker.sock

