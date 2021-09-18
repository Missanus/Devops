FROM nginx:latest

COPY ./index2.html /usr/share/nginx/html
COPY ./about2.html /usr/share/nginx/html


EXPOSE 80
EXPOSE 8080



CMD ["nginx", "-g", "daemon off;"]
