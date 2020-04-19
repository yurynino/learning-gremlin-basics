# Commands to install Gremlin with Docker

### Get nginx image
```shell
$ docker pull nginx
```

### Get gremlin image
```shell
$ docker pull gremlin/gremlin
```

### Verify docker images
```shell
$ docker images
```

## Nginx

### Create a page that will serve using nginx 
```shell
$ mkdir -p ~/docker-nginx/html
$ cd ~/docker-nginx/html
```
```html
<html>
  <head>
    <title>gremlin with docker</title>
  </head>
  <body>
    <div class="container">
      <h1>Hello it is your nginx container speaking!</h1>
    </div>
  </body>
</html>
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