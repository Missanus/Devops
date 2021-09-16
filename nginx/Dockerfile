FROM alpine
MAINTAINER missanchetouani@gmail.com

COPY . /WORKDIR_ALPINE
wORKDIR /WORKDIR_ALPINE

RUN apt-get install -y curl
RUN apt-get install nginx


RUN adduser -S devops		 
RUN addgroup -S devops

EXPOSE 8883

RUN python --version
CMD ["python" ,"hello_world.py"]
