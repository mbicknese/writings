---
title: Adding MongoDB to your Docker stack
date: 2018-05-08
tags: docker, security
---

Setting up Mongodb for your Docker stack sounds simple enough. Yet there are a few caveats you should know about. One can simply launch Mongo through Docker by running

    docker run --name insecure-mongo -d -p 27017:27017 mongo
    
Or add it to your `docker-compose.yml` file with

    services:
        mongo:
            image: mongo:3.6
            ports:
              - 27017:27017

## Authentication

Now you can connect to it locally at `localhost:27017`. At which point you'll notice you're being greeted with the nice message "WARNING: Access control is not enabled for the database". So we'll probably want to fix that for our production setup. Luckily the official Mongo Docker image comes with a smart startup script. By setting the environment variables `MONGO_INITDB_ROOT_USERNAME` and `MONGO_INITDB_ROOT_PASSWORD` we can secure our database.

Since we care about security. Use Docker secrets instead of exposing your password through the environment variable. In the case of MongoDB, all we need to do is set the path of the secret file using another enviroment variable `MONGO_INITDB_ROOT_PASSWORD_FILE`. Setting it to `/var/run/root_password` and mapping a secret to the container should get us up and running.

> **NOTE** CLI secrets only work if you're running docker in [swarm mode][1]

First, to create the secret

    echo "PickAStrongPassword" | docker secret create mongo_root_password -
    
Now we can start the service with our username and password

    docker service create --name secure-mongo \
        -d \
        -p 27017:27017 \
        -e MONGO_INITDB_ROOT_USERNAME=admin \
        -e MONGO_INITDB_ROOT_PASSWORD_FILE=/var/run/root_password \
        --secret source=mongo_root_password,target=/var/run/root_password \
        mongo

We can verify our securely running mongo instance by checking running processes. In this case we should see the `--auth` argument for our monog process.

    docker exec `docker ps --filter "name=secure-mongo" --format "{{.ID}}"` ps aux

Or in your compose file

    services:
        mongo:
            image: mongo:3.6
            ports:
              - 27017:27017
            environment:
                MONGO_INITDB_ROOT_USERNAME: admin
                MONGO_INITDB_ROOT_PASSWORD_FILE: /var/run/root_password
            secrets:
              - source: mongo_root_password
                target: root_password
                
    secrets:
        mongo_root_password:
            external: true
            
## Access
Up until now we've exposed the port for easy access. And since mongo 3.6 it binds to all available IP addresses. Meaning that if we don't put it behind a firewall, or configure it properly, it will be exposed to the outside world. So make sure you have a dedicated network for your stack. And ensure MongoDB only listens on that interface.

[1]: https://docs.docker.com/engine/swarm
