#### lydockerddive

- Directory Structure
```
cd /var/lib/docker
cat repositories-devivemapper | python -m json.tool //get all images information

cd conteiners
rm -rf * //delete all containers
```
Then,restart docker:
```
service docker restart
```

- Services That Run on Startup
open .bashrc, Add
```
/sbin/service httpd start
```
And this should be self start.
After running a container,
```
docker ps
```
to get container name, and use
```
docker inspect containername | grep IPAddress
```
to get ip.
```
docker logs containername
```
to get logs
```
curl http://containerip
```
to show the content of apache main page of the container

Or, we can create our own shell script instead of using .bashrc file
create a container, then
```
/usr/bin
vim runhtp.sh
```
in the sh, we add
```
#! /bin/bash

rm -rf /run/httpd/*
exec /usr/sbin/apachectl -D FOREGROUND
```
Edit privilege:
```
chmod 755 /usr/bin/runhrrp.sh
```
Edit .bashrc file,add
```
/user/bin/runhttp.sh
```

- Tying It Together
create a Dockerfile
```
FROM centos:centos6
MAINTAINER Ke<>
RUN yum -y update; yum clean all
RUN yum -y install httpd
RUN echo "New site" >>/var/www/html/index.html
EXPOSE 80
RUN echo "/sbin/service http start" >> /root/.bashrc
```

- Adding External Content
Add some files and build. Add the following in Dockerfile
```
ADD filename /var/www/html/test.html
```
- Advanced Container Network Management
```
ip link add br10 type bridge
ip addr add 10.10.100.1/24 dev br16
ip link set br10 up
```

- Exercise: Adding External Content to Containers
 Create a docker container name 'local_vol' from the 'centos:6' image. The container should be created in interactive mode, attached to the current terminal and running the bash shell. Finally create the container with a volume (or directory) called 'containerdata' so that the system will automatically create the directory/mount when the container starts.
```
docker run -it --name="local_vol" -v /containerdata centos:6 /bin/bash (Another syntax is --name containername)
```
Exit the container. This time, create another container called 'remote_vol' with the same container configuration except when creating the volume in the container, link the volume name 'mydata' to the underlying host directory structure created in Step #1.
```
docker run -it --name="remote_vol" -v /home/user/docker/mydata:/mydata centos:6 /bin/bash
```
- Interactive Shell Control

Direct run a container's process
```
docker exec -i -t containername /usr/bin/top
```

- Previous Container Management
Delete docker containers older than 7 days:
```
docker ps -a | grep "7 days ago" | awk '{print $1}'| xargs docker rm
```
- Container Routing
Add a route in host machine:
```
route add -net 172.17.0.0 netmask 255.255.255.0 gw 192.168.1.35
```
- Sharing Container Resources
Build first container:
```
docker run -d -i -t -v /data --name DATA1 img:tag /bin/bash
```
build second container:
```
docker run -d -i -t --volumes-from DATA1 --name DATA2 img:tag /bin/bash
```

- Container Linking and Communication
Build first container:
```
docker run -d -i -t --name mywebserver img:tag /bin/bash
```
build second container:
```
docker run -d -i -t  --name secondcontainer --link mywebserver:myalias img:tag /bin/bash
```
- Taking Control of Ports
```
docker run -d -i -t -p 80:80(hostip:container mapping ip) --name mywebserver img:tag /bin/bash
```
Or automatic assign port 80 using -P
```
docker run -d -i -t -P --name mywebserver img:tag /bin/bash
```
We can visit apache main page by
```
localhost:12345
```
Or use ip address

We can also bind local ip

```
docker run -d -i -t -p 127.0.0.1:5000:80 --name mywebserver img:tag /bin/bash
```
- Five Useful Docker CLI Commands
cp
```
docker cp containername:/etc/yum.conf targetfolder
```
diff (compare conatainer) A=add D=delete C=changed
```
docker diff containername
```
events (requires real time monitering) need to open another window and start a new container
```
docker events
docker events --since '2015-12-21'
```
history(not for container,but for image)
```
docker history centos:latest
```

