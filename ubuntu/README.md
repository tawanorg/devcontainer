Dockerfile for JavaScript/Node Projects

## Dependencies

- Git
- cURL
- NVM and Node 14
- NPM, Yarn
- Vim and tmux

## Prerequisites

I assume you have installed Docker and it is running.

See the [Docker website](http://www.docker.io/gettingstarted/#h_installation) for installation instructions.

## Build

Steps to build a Docker image:

1.  Clone this repo
2.  Build the image

        docker build -t @arkade/ubuntu -f Dockerfile .

3.  Create a `Dockerfile` file at your root project

        FROM @arkade/ubuntu:latest

        # Check versions

        RUN node -v
        RUN npm -v
        RUN yarn -v

        # Create a new project folder

        WORKDIR /home/docker/website

        # Create the working directory, including the node_modules folder for the sake of assigning ownership in the next command

        RUN mkdir -p node_modules

        # Copy package.json, package-lock.json

        # Copying this separately prevents re-running npm install on every code change.

        COPY package\*.json .

        # # Install Global dependencies.

        RUN yarn global add firebase-tools

        # # Install dependencies.

        RUN yarn

        # Bundle app source

        COPY . .
