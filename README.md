(using Ubuntu 18.04 LTS Minimal) 

## 1. Pre-Installation
```
$ sudo -i
# apt update
# apt install -y vim git
# git clone https://github.com/brtown/docker-squid-otp.git
```
## 2. Docker
### Install
```
# apt install -y docker docker-compose
```
### Verify
```
# docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
0e03bdcc26d7: Pull complete 
Digest: sha256:8c5aeeb6a5f3ba4883347d3747a7249f491766ca1caa47e5da5dfcf6b9b717c0
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.
:
:
:

```
## 3. FreeRADIUS
### Create a Docker image
```
# cd docker-squid-otp/freeradius/
# chmod 755 docker-entrypoint.sh
# docker build -t freeradius-otp:latest .
# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
freeradius-otp      latest              159e3d1b5e5b        4 seconds ago       14.3MB
alpine              latest              d6e46aa2470d        3 weeks ago         5.57MB
hello-world         latest              bf756fb1ae65        10 months ago       13.3kB
```
### Run created image
```
# docker run -it -d freeradius-otp:latest
# docker ps
CONTAINER ID        IMAGE                   COMMAND                  CREATED             STATUS              PORTS               NAMES
d7e44158eae2        freeradius-otp:latest   "/docker-entrypoint.…"   11 seconds ago      Up 10 seconds       1812-1813/udp       sharp_kepler
```
### Verity One-Time-Password
```
# docker exec -it sharp_kepler sh
/ # adduser --disabled-password raduser
/ # su - raduser
$ radtest raduser 821523 localhost 1812 KJjYSXS6KdGWos
Sent Access-Request Id 36 from 0.0.0.0:55240 to 127.0.0.1:1812 length 77
	User-Name = "raduser"
	User-Password = "821523"
	NAS-IP-Address = 172.17.0.2
	NAS-Port = 1812
	Message-Authenticator = 0x00
	Cleartext-Password = "821523"
Received Access-Accept Id 36 from 127.0.0.1:1812 to 127.0.0.1:55240 length 20
```

### Complete
```
$ exit
/ # exit
# docker stop sharp_kepler
# docker rm sharp_kepler  
```
## 4. Squid
### create a Docker image
```
# cd ~/docker-squid-otp/squid/
# docker build -t squid-otp:latest .
# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
squid-otp           latest              bad935e6d7d7        50 seconds ago      17.6MB
freeradius-otp      latest              29dcb3c203f4        17 minutes ago      14.3MB
alpine              latest              d6e46aa2470d        3 weeks ago         5.57MB
hello-world         latest              bf756fb1ae65        10 months ago       13.3kB
```
## 5. Google Authenticator (on Host)
### Install
```
# apt install -y libpam-google-authenticator
```
### Verify
```
# adduser --disabled-password --gecos "" proxyuser

# su proxyuser
$ google-authenticator
$ exit
```
## 6. Start Containers
```
# cd ~/docker-squid-otp/
# docker-compose up -d
Creating network "squid-otp_default" with the default driver
Creating squid-otp_freeradius_1 ... done
Creating squid-otp_squid_1      ... done
# docker ps
CONTAINER ID        IMAGE                   COMMAND                  CREATED              STATUS              PORTS                              NAMES
d282526184ad        squid-otp:latest        "/usr/sbin/squid -NYC"   About a minute ago   Up About a minute   0.0.0.0:8080->8080/tcp             docker-squid-otp_squid_1
ff277791904d        freeradius-otp:latest   "/docker-entrypoint.…"   About a minute ago   Up About a minute   0.0.0.0:1812-1813->1812-1813/udp   docker-squid-otp_freeradius_1
```
### Create a new user
```
# adduser --disabled-password --gecos "" newuser
# su newuser
$ google-authenticator
$ exit
# docker-compose restart
```
