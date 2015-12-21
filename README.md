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
``
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
