# Week 1 — App Containerization

## Homework Checklist:

## Homework Challenges:
### Run the dockerfile CMD as an external script
### Push and tag a image to DockerHub (they have a free tier)
### Use multi-stage building for a Dockerfile build
### Implement a healthcheck in the V3 Docker compose file
### Research best practices of Dockerfiles and attempt to implement it in your Dockerfile
### Learn how to install Docker on your localmachine and get the same containers running outside of Gitpod / Codespaces
### Launch an EC2 instance that has docker installed, and pull a container to demonstrate you can run your own docker processes. 



## Scratch notes
### Running the flask backend application on the gitpod host machine:

- This actually will run in a docker container, but to test it out on the gitpod host, do this:

### Install flask:

```sh
cd backend-flask
export FRONTEND_URL="*"
export BACKEND_URL="*"
python3 -m flask run --host=0.0.0.0 --port=4567
cd ..
```

### To navigate to the application:
- make sure to unlock the port on the port tab
- open the link for 4567 in your browser
- append to the url to `/api/activities/home`
- you should get back json

- To do this in docker (instead of directly on the gitpod host machine), we docker build the dockerfile:
```sh
docker build -t  backend-flask ./backend-flask
```

- Run the image interactively:
```sh
docker run --rm -p 4567:4567 -it backend-flask
```

- Similar to the testing above, we need to set environment variables
FRONTEND_URL="*"
BACKEND_URL="*"

- So now we can include them in the docker run command with the `-e` flag:
```docker
docker run --rm -p 4567:4567 -e FRONTEND_URL -e BACKEND_URL -it backend-flask
```
- In the above, the env vars are inherited from whatever host machine you're running from. Or you can just set the env vars in the run:
```docker
docker run --rm -p 4567:4567 -e FRONTEND_URL='*' -e BACKEND_URL='*' -it backend-flask
```