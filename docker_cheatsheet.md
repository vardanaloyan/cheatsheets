`docker images` -- list docker images

`docker run -ti ubuntu:latest bash` -- run the docker

`docker ps` -- list active dockers

`docker ps -l` -- show last exited container

`docker rmi image_name:tag/image_id` -- remove image

![Docker Flow](./data/docker_flow.png)

* `docker ps -l` -- show last exited container
* copy ID of the last exited container
* `docker commit ${ID}` -- create new image, this will return sha, need to copy
* `docker tag ${sha} <New Name>` -- to give a name to the image

This can be achieved by one command
* `docker commit <image name> <new image name>`


`docker run --rm ...` -- after completing run, docker will remove the container, instead of manually
typing `docker rm <container_name>`

`docker run -ti ubuntu bash -c "sleep 3; echo all done"` -- for executing more than one commands

---

`docker run -d -ti ubuntu bash` -- for detached run

`docker ps` -- take name

`docker attach ${name}` -- return back to the session

`ctrl+pq` -- jump away from the session. Detach the session

---

`docker ps` -- take the container name
`docker exec -ti ${name} bash` -- Execute a new process in the running container

---

`docker run --name ...` -- Give a name to a container

`docker logs ${container_name}` -- See the logs of container

`docker kill ${container_name}` -- Stop the running container

Stopped containers still exist, need to explicitly remove them

`docker rm ${container_name}` -- Remove the stopped container

---

### Memory limitations

`docker run --rm --memory 200m my-image-2 free -m` -- ram limitations

---

### Exposing ports

`docker run --rm -ti -p 45678:45678 -p 45679:45679 --name echo-server ubuntu bash`

`-p outside-port:inside-port` -- publish port 

`apt-get update && apt-get install -y netcat`

`nc -lp 45678 | nc -lp 45679` -- listen port 45678 and write output in 45679 port

On local machine open two terminals

`nc localhost 45678`

`nc localhost 45679`

If needed to run netcat from the docker, instead of 
localhost, write `host.docker.internal`

If we fill only one port, it will be port in docker container, and local machine port
docker will choose automatically.

`docker run --rm -ti -p 45678:45678 -p 45679:45679 --name echo-server ubuntu bash`

`docker port echo_server` -- to see which ports were chosen

---

### We can also specify the protocol tcp/udp

`docker run --rm -ti -p 45678/udp --name echo-server ubuntu bash`

`docker port echo-server` -- find attached external port

```bash
Vardan_Aloyan@EPAMYERW0183 ~ % docker port echo-server
45678/udp -> 0.0.0.0:52049
```
Then we are listening to that port

```bash
Vardan_Aloyan@EPAMYERW0183 ~ % nc -u localhost 52049

```
Also in docker container

```bash
root@6854bcbe1e51:/# nc -ulp 45678

```

### Container networking


`docker network ls` -- For listing default networks

```bash
Vardan_Aloyan@EPAMYERW0183 ~ % docker network  ls                                             
NETWORK ID     NAME       DRIVER    SCOPE
161508f8e0b6   bridge     bridge    local
a74e12faec2e   host       host      local
73ee0dac82c2   learning   bridge    local
3d8267ba71eb   none       null      local
```

`docker network create learning` -- create network called `learning`

Lets run container called catserver

`docker run -ti --net learning --name catserver ubuntu-v1 bash` -- run docker with specific network 

```bash
root@4931559ca1bf:/# ping catserver
PING catserver (172.18.0.2) 56(84) bytes of data.
64 bytes from 4931559ca1bf (172.18.0.2): icmp_seq=1 ttl=64 time=0.070 ms
64 bytes from 4931559ca1bf (172.18.0.2): icmp_seq=2 ttl=64 time=0.100 ms
64 bytes from 4931559ca1bf (172.18.0.2): icmp_seq=3 ttl=64 time=0.184 ms
```

Now lets run container called dogserver with the same network `learning`

`docker run -ti --net learning --name dogserver ubuntu-v1 bash`

Now if we can ping `catserver` from `dogserver` container and vice versa

```bash
root@d01498f046d6:/# ping catserver
PING catserver (172.18.0.2) 56(84) bytes of data.
64 bytes from catserver.learning (172.18.0.2): icmp_seq=1 ttl=64 time=0.357 ms
64 bytes from catserver.learning (172.18.0.2): icmp_seq=2 ttl=64 time=0.323 ms
64 bytes from catserver.learning (172.18.0.2): icmp_seq=3 ttl=64 time=0.681 ms
```

Now we can connect to eachother via network.

Catserver

```bash
root@4931559ca1bf:/# nc -lp 1234
Hello from dogserver
```

Dogserver

```bash
root@d01498f046d6:/# nc catserver 1234
Hello from dogserver
```

#### Add one network to another network

`docker network create catsonly` -- create new network

`docker network connect catsonly catserver` -- connect container to a network

`catserver` container connected to a `catsonly` network, so it will see containers in that network

### Legacy Linking (older way)

Legacy linking only operates in one direction.

* Links all ports, though only one way
* Secret environment variables are shared only one way

`docker run -ti -e SECRET=thesecretvalue --name catserver ubuntu-v1 bash` -- run catserver container with predefined environment variable


`docker run -ti --link catserver  --name dogserver ubuntu-v1 bash` -- run dogserver container and link it with catserver container

We can see env variables of catserver container. This can be security issue.

```bash
root@7f6d94c4c0af:/# env
CATSERVER_NAME=/dogserver/catserver
HOSTNAME=7f6d94c4c0af
PWD=/
CATSERVER_ENV_SECRET=thesecretvalue

```

catserver can not see dogserver, because linking works in one way (netcat)

---

### Volumes

* Persistent
* Ephemeral

Sharing a folder with the host

`docker run -it -v /Users/Vardan_Aloyan/test:/shared_folder ubuntu-v1 bash` -- share folder `test`, it will appear in docker as `shared_folder`

Sharing Data between containers

`docker run -ti -v /shared_data ubuntu-v1 bash --name cont1` -- create volume in container

`docker run -ti --volumes-from cont1 ubuntu-v1 bash` -- attach valumes from container's valumes

When we exit original container, volume still exist in the second container until we close the second container. Or we can exit the first container, then create the third container attaching volumes from the second container.

### Docker registries

`docker search ubuntu` -- search for images

`docker pull debian:sid` -- pull image

`docker login` -- login hub.docker.com

`docker tag debian:sid aloyan/test-image` -- tag with new name

`docker push aloyan/test-image` -- push new image

---

### Building docker images

Simple Dockerfile

```bash
FROM busybox
RUN echo "building simple docker image"
CMD echo "hello world!"
```

`docker build -t hello .` -- build docker image and tag the name "hello"

---

Build another image with this Dockerfile

```bash
FROM debian:sid
RUN apt-get -y update
RUN apt-get install -y nano
CMD ["/bin/nano", "/tmp/notes"]
```

`docker build -t example/nanoer . ` -- build the image

`docker run --rm -ti example/nanoer` -- run the container

---

```bash
FROM example/nanoer
ADD notes.txt /notes.txt
CMD ["/bin/nano", "/notes.txt"]
```
`docker build -t example/notes . ` -- build the image

`docker run --rm -ti example/notes` -- run the container



source: [LinedIn learning](https://www.linkedin.com/learning/learning-docker-2018/the-docker-flow-images-to-containers?u=2113185)
