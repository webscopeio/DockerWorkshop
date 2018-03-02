# Docker workshop

## Prerequisites:

### Install Docker:

Check your docker version
```sh
$ docker -v
```
You should have `Docker version 17.12.0-ce` or newer.

If you don't have docker installed. You can find instructions how to install docker on your machine in following links:
 * [Mac](https://docs.docker.com/docker-for-mac/install/)
 * [Fedora](https://docs.docker.com/install/linux/docker-ce/fedora/)
 * [Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
#### Post installation for Linux users
If you don’t want to use `sudo` when you use the `docker` command, create a Unix group called `docker` and add users to it. 
```sh
$ sudo groupadd docker # create docker group
$ sudo usermod -aG docker $USER # add $USER to the docker group
```
In case you want to start docker deamon on boot
```sh
$ sudo systemctl enable docker
```
More in: [Linux-postinstall](https://docs.docker.com/install/linux/linux-postinstall/) 

#### Create Docker account

If you don’t have a Docker account, sign up for one in [Docker Cloud](https://cloud.docker.com). Make note of your username.

After that try to log in.

[Login reference](https://docs.docker.com/engine/reference/commandline/login/)
```sh
$ docker login
```
## Hello Webscope
To download `majodurco/hello_webscope` docker image from [Docker Cloud](https://cloud.docker.com/app/majodurco/repository/docker/majodurco/hello_webscope)

[Pull reference](https://docs.docker.com/engine/reference/commandline/pull/)
```sh
$ docker pull majodurco/hello_webscope
```
To show list of your local images

[Images reference](https://docs.docker.com/engine/reference/commandline/images/)
```sh
$ docker images
```
Now you should see `majodurco/hello_webscope` in list of local images
```
REPOSITORY                 TAG      IMAGE ID          CREATED        SIZE
majodurco/hello_webscope   latest   <IMAGE>           <TIME>         4.15MB
```
In order to run the image use

[Run reference](https://docs.docker.com/engine/reference/commandline/run/)
```sh
$ docker run majodurco/hello_webscope
```
Do you recognize the logo? :sunglasses:

_NOTE_ 
We could just `run` command and docker will automatically donwload all images needed. We use `pull` just for educational purposes.

Let's examine this image closely.

[Run reference](https://docs.docker.com/engine/reference/commandline/run/)
```sh
$ docker run -it majodurco/hello_webscope sh
```
So what we just did? We run specific command `sh` inside `majodurco/hello_webscope` image and with that we created **container**(process on the host machine).

If you still haven't close shell yet you can see one container running

[Ps reference](https://docs.docker.com/engine/reference/commandline/ps/)
```sh
$ docker ps

CONTAINER ID  IMAGE                     COMMAND  CREATED   STATUS     PORTS  NAMES
<ID>          majodurco/hello_webscope  "sh"     <TIME>    Up <TIME>         <NAME>

```
So here you can see a list of all containers which are now running on your machine. You can also check out `docker ps -a` which shows you all running and also exited containers.

#### Dockerfile

A Dockerfile is a text file that is kind of a recipe for docker to know how to build an image. Using docker build users can create an automated build that executes several command-line instructions in succession.
You can find `Dockerfile` to `majodurco/hello_webscope` image in `/hello_webscope/Dockerfile`.

`FROM alpine` [FROM reference](https://docs.docker.com/engine/reference/builder/#from) 
sets [alpine](https://hub.docker.com/_/alpine/) as base image, which is image on which we build our image.

`ADD webscope_logo.txt /webscope_logo.txt` [ADD reference](https://docs.docker.com/engine/reference/builder/#add)
Add `webscope_logo.txt` which you can see in `hello_webscope` directory inside image's root directory with the same name.

`CMD /bin/cat webscope_logo.txt` [CMD reference](https://docs.docker.com/engine/reference/builder/#cmd)
Sets specific command as default for container execution.

## Create your own image from scratch

We are going to containerize [create-react-app](https://github.com/facebook/create-react-app).
In order to do so we need to create `Dockerfile` for docker to know how build our new image.

This is how it may looks:
```docker
FROM node:alpine

RUN npx create-react-app my-app
WORKDIR my-app
EXPOSE 3000
CMD npm start
```
`FROM node:alpine` [FROM reference](https://docs.docker.com/engine/reference/builder/#from)
we've seen something similar before just instead of `alpine` we now have `node:alpine` [Node docker hub](https://hub.docker.com/_/node/) it's because `alpine` image doesn't have node.js installed. We could play with it install it and use that, but why when somebody did it before us.

`RUN npx create-react-app my-app` [RUN reference](https://docs.docker.com/engine/reference/builder/#run) 
with `RUN` you can execute any command on top of the current image and commit the changes. These commands run only durring build phase of an image.

`WORKDIR my-app` [WORKDIR reference](https://docs.docker.com/engine/reference/builder/#workdir) 
we just set `/my-app` as working directory so in instructions like `RUN`, `CMD`, `ADD` we don't have to specify path from the root `/`.

`EXPOSE 3000` [EXPOSE reference](https://docs.docker.com/engine/reference/builder/#expose) 
this instruction does not actually publish the port. It functions as a type of documentation between the person who builds the image and the person who runs the container.

`CMD npm start` [CMD reference](https://docs.docker.com/engine/reference/builder/#cmd)
Run `npm start` as default command in container's execution.

That should be enough for our `Dockerfile` now let's build the image.

[Build reference](https://docs.docker.com/engine/reference/commandline/build/)
```sh
docker build -t my-app <DIR_WITH_DOCKERFILE>
```
Good, you've just build our docker image. You can check it out with `$ docker images`. Output should be similar to:
```sh
$ docker images

REPOSITORY  TAG     IMAGE ID     CREATED   SIZE
my-app      latest  <IMAGE_ID>   <TIME>    271MB
```

Alright let's try to run this image
```sh
$ docker run my-app
```
It seems everything works fine, so try to open `localhost:3000` in your browser to check if it is working. 

Whoops, not so good right? Problem is we didn't map host's port with container's port. To do so we need to add some options to our `run` command.

```sh
$ docker run -it -p 3001:3000 my-app
```
Now try `localhost:3001` in your browser. It seems to be working :tada:. We use port `3001` just to show which port specify container and which host machine.

Now what if we want to change something in the code and see changes in container? We can change some files in container but remember these changes will be **discarded** after you kill this container!
There're are multiple ways how to solve this. 
We are going to make some changes and then put these changes into image. So we need to change our `Dockerfile` if we want to add something more to our image.

```docker
FROM node:alpine

RUN npx create-react-app my-app

WORKDIR my-app

ADD src src # add this line

EXPOSE 3000

CMD npm start
```

Where directory containing `Dockerfile` looks like this: 
```
├── Dockerfile
└── src
    ├── App.css
    ├── App.js
    ├── App.test.js
    ├── index.css
    ├── index.js
    ├── registerServiceWorker.js
    └── webscope.svg
```
You can find `src` directory in `create_react_app/src/`.

So we `ADD` this `src` directory into our image. This directory contain the changes we want. Now in order to see the changes we need to rebuild the image. We use same command as before.

[Build reference](https://docs.docker.com/engine/reference/commandline/build/)
```sh
docker build -t my-app <DIR_WITH_DOCKERFILE>
```

In case we are developing the application and don't want to build the image every time we make a change. So how we can deal with this?
There comes the [Bind mount](https://docs.docker.com/storage/bind-mounts/) to save us. Let's say we just want to develop the app on the host but run it in a container without need of rebuilding the image on every change. In case of `create-react-app` we can do the following.

Your host working directory should be the root directory of your application.
```sh
$ docker run -it -p 3000:3000 -v type=bind,source=$PWD,target=/app node:alpine sh
# in container's shell
$ cd /app
$ yarn # if you haven't instaled packages yet
$ yarn start
```
Now your host working directory (`$PWD`) is the same as `/app` directory inside the container. You can change the code of the app on your host and still see the changes from container :tada:.

Ok, so if we are satisfied with our image we can publish it for other people to also enjoy it.

We need prefix the image with our `docker username`, so how you've registered in [Docker Cloud](https://cloud.docker.com).

[Tag reference](https://docs.docker.com/engine/reference/commandline/tag/)
```sh
$ docker tag <LOCAL_IMAGE_NAME>[:TAG] <USERNAME>/<IMAGE-NAME>[:TAG] 
```
So I will write
```sh
$ docker tag my-app majodurco/my-app
```

And finally push this image with your username to the cloud.
Make sure you log in before `$ docker login`.
```sh
$ docker push majodurco/my-app
```

Now everybody can run your image you've just created.
```sh
$ docker run -it -p 3001:3000 majodurco/my-app
```