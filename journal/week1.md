# Week 1 — App Containerization

## Containerize the Backend
### Run Python
```sh
cd backend-flask
pip3 install -r requirements.txt
export FRONTEND_URL="*"
export BACKEND_URL="*"
python3 -m flask run --host=0.0.0.0 --port=4567
cd ..
```

- Make sure to unlock port `4567` in the **PORTS** tab of your VSCode
- Open the link for `4567` in your browser
- Append `/api/activities/home` to the url
- You should get back json

### Add Dockerfile
Note that this is also a good example for containerizing a flask application.
Create a file here: `backend-flask/Dockerfile`

```Dockerfile
FROM python:3.10-slim-buster

WORKDIR /backend-flask

COPY requirements.txt requirements.txt

RUN pip3 install -r requirements.txt

COPY . .

ENV FLASK_ENV=development

EXPOSE ${PORT}

CMD [ "python3", "-m", "flask", "run", "--host=0.0.0.0", "--port=4567" ]
```

### Run `unset`
You should ensure to unset the env vars `FRONTEND_URL` & `BACKEND_URL` so they don't get in the way of the container's values.
```sh
unset FRONTEND_URL
unset BACKEND_URL
```

### Build Container

```
docker build -t backend-flask ./backend-flask
```

### Run Container
Run

```sh
docker run --rm -p 4567:4567 -it -e FRONTEND_URL='*' -e BACKEND_URL='*' backend-flask
```

Run in background
```sh
docker container run --rm -p 4567:4567 -d -e FRONTEND_URL='*' -e BACKEND_URL='*' backend-flask
```

Return the container id into an Env Var
```
CONTAINER_ID=$(docker run --rm -p 4567:4567 -d backend-flask)
```
### Send `curl` to Test Server

```
curl -X GET http://localhost:4567/api/activities/home -H "Accept: application/json" -H "Content-Type: application/json"
```

### Gain Access to a Container
```
docker exec CONTAINER_ID -it /bin/bash
```

### Overriding Ports
```
docker run --rm -p 4567:4567 -e FLASK_ENV=production -e PORT=8080 -e FRONTEND_URL='*' -e BACKEND_URL='*' -it backend-flask
```

## Containerize the Frontend

### Run NPM Install
Ensure to run the `npm install` command before building the container since it needs to copy the contents of node_modules

```
cd frontend-react-js
npm i
```

### Create Dockerfile
Create the file here: `frontend-react-js/Dockerfile`

```Dockerfile
FROM node:16.18

ENV PORT=3000

COPY . /frontend-react-js

WORKDIR /frontend-react-js

RUN npm install 

EXPOSE ${PORT}

CMD ["npm", "start"]
```

### Build Container
```
docker build -t frontend-react-js ./frontend-react-js
```

### Run Container
```
docker run -p 3000:3000 -d frontend-react-js
```


## Multiple Containers
### Create a `docker compose` file

Create `docker-compose.yml` at the root of your project

```docker-compose
version: "3.8"
services:
  backend-flask:
    environment:
      FRONTEND_URL: "https://3000-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
      BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
    build: ./backend-flask
    ports:
      - "4567:4567"
    volumes:
      - ./backend-flask:/backend-flask
  frontend-react-js:
    environment:
      REACT_APP_BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
    build: ./frontend-react-js
    ports:
      - "3000:3000"
    volumes:
      - ./frontend-react-js:/frontend-react-js

# the name flag is a hack to change the default prepend folder
# name when outputting the image names
networks:
  internal-network:
    driver: bridge
    name: cruddur
```
