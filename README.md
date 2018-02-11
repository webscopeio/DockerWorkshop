# Docker workshop

## Prerequisites:

#### Install Docker:

Check your docker version
```sh
$ docker -v
```
You should have `Docker version 17.12.0-ce` or later.

If you don't have docker installed. You can find instructions how to install docker on your machine in following links:
 * [Mac](https://docs.docker.com/docker-for-mac/install/)
 * [Fedora](https://docs.docker.com/install/linux/docker-ce/fedora/)
 * [Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
##### Post installation for Linux users
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

If you don’t have a Docker account, sign up for one at cloud.docker.com. Make note of your username.

After that try to log in.

[Login reference](https://docs.docker.com/engine/reference/commandline/login/)
```sh
$ docker login
```
## Hello Webscope
To download `majodurco/hello_webscope` docker image from [Docker cloud](https://cloud.docker.com/app/majodurco/repository/docker/majodurco/hello_webscope)

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
majodurco/hello_webscope   latest   <SOME_IMAGE_ID>   <SOME_TIME>    4.15MB
```
In order to run image use

[Run reference](https://docs.docker.com/engine/reference/commandline/run/)
```sh
$ docker run majodurco/hello_webscope
```
Do you recognize the logo? :sunglasses:

_NOTE_ 
We could just `run` command and docker will automatically donwload all images needed. We use `pull` just for educational purposes.

#### Close examination

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

##### Dockerfile

A Dockerfile is a text file that is kind of recipe for docker to know how to build an image. Using docker build users can create an automated build that executes several command-line instructions in succession.
The Dockerfile to `majodurco/hello_webscope` image you can find in `/hello_webscope/Dockerfile`.

`FROM alpine` [FROM reference](https://docs.docker.com/engine/reference/builder/#from) sets [alpine](https://hub.docker.com/_/alpine/) as base image, which is image on which we build our image.

`ADD webscope_logo.txt /webscope_logo.txt` [ADD reference]( https://docs.docker.com/engine/reference/builder/#add)
Add `webscope_logo.txt` which you can see in `hello_webscope` dir inside image's root dir with same name.

`CMD /bin/cat webscope_logo.txt` [CMD reference](https://docs.docker.com/engine/reference/builder/#cmd)
Sets specific command as default for container execution.

## Create your own image from scratch