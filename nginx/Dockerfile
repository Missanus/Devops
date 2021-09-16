FROM alpine
MAINTAINER missanchetouani@gmail.com

COPY . /WORKDIR_ALPINE
wORKDIR /WORKDIR_ALPINE

RUN apt install -y curl \
 
RUN apt install nginx




RUN adduser -S devops		 
RUN addgroup -S devops

RUN set -x 
    && tempDir="$(mktemp d)" # repetoire temporaire /tmp/onfolder

	

EXPOSE 8883

RUN python --version
CMD ["nginx" ,"-g", "daemon off;"]

