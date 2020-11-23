# Commands to install Gremlin with Kubernetes

> This section explains how to install and configure Gremlin with an Amazon EKS cluster. Wee need to install before:

- [ ] [Install aws cli](#aws_cli) A command line tools for working with AWS services.
- [ ] [Install eksctl](#install-eksctl) A command line tool for working with EKS clusters.
- [ ] [Install kubectl](#install-kubectl) A command line tool for working with kubernetes clusters.
- [ ] [Install helm](#install-helm)

## Install awscli

```shell
$ curl "https://awscli.amazonaws.com/AWSCLIV2.pkg" -o "AWSCLIV2.pkg"
$ sudo installer -pkg AWSCLIV2.pkg -target
```

### Install awscli

```shell
$ aws configure
AWS Access Key ID [None]: AKIAIOSFODNN7EXAMPLE
AWS Secret Access Key [None]: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
Default region name [None]: us-west-2
Default output format [None]: ENTER
```

## Install kubectl

```shell
$
```

### Install eksctl

```shell
$ brew tap weaveworks/tap
```

Install the Weaveworks Homebrew tap.

```shell
$ brew install weaveworks/tap/eksctl
```

If eksctl is already installed, run the following command to upgrade:

```shell
$ brew upgrade eksctl && brew link --overwrite eksctl
```

Test that your installation was successful with the following command.

```shell
$ eksctl version
```

### Install Helm

```shell
$ docker images
```

### Create a page that will serve using nginx

```shell
$ mkdir -p ~/docker-nginx/html
$ cd ~/docker-nginx/html
```

### Create an nginx docker container

```shell
$ docker run -l service=nginx \
    --name docker-nginx \
    -p 80:80 -d \
    -v ~/docker-nginx/html:/usr/share/nginx/html \
    nginx
```

### Verify that nginx docker is running

```shell
$ docker ps -a
```

### Verify that nginx container is running

```html
In a browser http://localhost
```

## Gremlin

### Set environment variables:

```shell
$ export GREMLIN_TEAM_ID=xxxxxxxx-xxxx-xxxx-xxxxxxxx
$ export GREMLIN_TEAM_SECRET=xxxxxxxx-xxxx-xxxx-xxxxxxxx
$ export GREMLIN_BYPASS_USERNS_REMAP=1
```

### Run gremlin daemon

```shell
    $ docker run -d --net=host \
    --cap-add=NET_ADMIN --cap-add=SYS_BOOT --cap-add=SYS_TIME \
    --cap-add=KILL \
    -v $PWD/var/lib/gremlin:/var/lib/gremlin \
    -v $PWD/var/log/gremlin:/var/log/gremlin \
    -v /var/run/docker.sock:/var/run/docker.sock \
    -e GREMLIN_TEAM_ID="$GREMLIN_TEAM_ID" \
    -e GREMLIN_TEAM_SECRET="$GREMLIN_TEAM_SECRET" \
    -e GREMLIN_BYPASS_USERNS_REMAP="$GREMLIN_BYPASS_USERNS_REMAP" \
    gremlin/gremlin daemon
```

### Verify that gremlin docker is running

```shell
$ docker ps -a
```

### Attack nginx container

```shell
$ sudo docker run -d \
    --net=host \
    --pid=host \
    --cap-add=NET_ADMIN \
    --cap-add=SYS_BOOT \
    --cap-add=SYS_TIME \
    --cap-add=KILL \
    -e GREMLIN_TEAM_ID="${GREMLIN_TEAM_ID}" \
    -e GREMLIN_TEAM_SECRET="${GREMLIN_TEAM_SECRET}" \
    -v /var/run/docker.sock:/var/run/docker.sock \
    -v /var/log/gremlin:/var/log/gremlin \
    -v /var/lib/gremlin:/var/lib/gremlin \
    gremlin/gremlin attack-container docker-nginx shutdown
```

### Verify that nginx docker is NOT running

```shell
$ docker ps -a
```

### Verify that nginx container is NOT running

```html
In a browser http://localhost
```

### Explore more Attacks!

- [Gremlin attacks](https://www.gremlin.com/docs/application-layer/attacks/)
