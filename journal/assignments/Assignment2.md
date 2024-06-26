# Homework Challenges
1. Run the dockerfile CMD as an external script
2. Push and tag an image to DockerHub(they have a free tier)
3. Use multi-stage building for a Dockerfile build
4. Implement a healthcheck in the V3 Docker compose file
5. Research best practices of Dockerfiles and attempt to implement it in your Dockerfile
6. Learn how to install Docker on your localmachine and get the same containers running outside of Gitpod/Codespaces
7. Launch an EC2 instance that has docker installed, and pull a container to demonstrate you can run your own docker processes.

## 1. Run the Dockerfile `CMD` as an External Script
A bash script file was created `backend-flask/docker_cmd.sh`.

Next, modifications to the Dockerfile were added:
- A `RUN` command to turn the earlier file into an executable.
- A final `CMD` file to run the executable bash script.

## 2. Push and Tag an Image to DockerHub
```
cd ./backend-flask
docker build -t backend-flask .
docker tag [IMAGE_ID] [REGISTRY_NAME]/backend-flask:1

docker login    # Enter username and password
docker push [REGISTRY_NAME]/backend-flask:1
```

