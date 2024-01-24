
# -------------- Deploy Front end application -------------- #/

# Dockerize project

touch Dockerfile

# Dockerfile Example

## Stage 1: Compile and Build angular codebase

FROM node:latest as build

WORKDIR /usr/local/app

COPY ./ /usr/local/app/

RUN npm install --force

RUN npm run build

## Stage 2: Serve app with nginx server

FROM nginx:latest

COPY --from=build /usr/local/app/dist/tagus /usr/share/nginx/html

EXPOSE 80

## important

CMD ["nginx", "-g", "daemon off;"]

# Build and publish to docker hub

docker build --push -t lutwrld/hospital-management:prod

to run for test : docker run -d -p 8080:80 lutwrld/hospital-management:prod

go to <<http://localhost:8080>

# Go to server using ssh

ssh username@your_vps_ip

# Pull docker image from hub

docker pull your_image_name:tag

NB : tap 'docker login'  if credentials are not stored

# Run docker container

docker run -d -p host_port:container_port --name your_container_name your_image_name:tag

# Access to the project

http://your_vps_ip:host_port

@byLutwrld
