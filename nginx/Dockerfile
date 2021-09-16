FROM ubuntu:14.04
MAINTAINER missanchetouani@gmail.com

COPY . /WORKDIR_ALPINE
wORKDIR /WORKDIR_ALPINE

RUN apt-get install -y curl
RUN apt-get install -y nginx



# https://www.cyberciti.biz/faq/create-a-user-account-on-ubuntu-linux/ to create user and keyssh
RUN sudo adduser devops # -d /home/devops -m -G sudo devops
#RUN passwd devops 

RUN set -x \ 
    tempDir="$(mktemp -d)" \
    mkdir -p /tmp/app \
    echo "temp dire path $tempDir"

	

EXPOSE 8883

#RUN python --version
CMD ["nginx" ,"-g", "daemon off;"]

