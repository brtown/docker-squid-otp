(using Debian GNU/Linux 10 (buster)) 

# 1. Pre-Installation
```
$ sudo -i
# apt update
# apt install -y vim git
```
# 2. Install Docker
Reference: https://docs.docker.com/engine/install/debian/

## Install
```
# apt update
# apt install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
# curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -
# apt-key fingerprint 0EBFCD88
# add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/debian \
   $(lsb_release -cs) \
   stable"

# apt update
# apt install docker-ce docker-ce-cli containerd.io
```
## Verify
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

#### 2. Clone repository
```
# git clone https://github.com/brtown/Squid-OTP.git
# cd Squid-OTP/
```

#### 3. Create a Docker Image of FreeRADIUS (including OTP Authentication with Google Authenticator)
```
# cd FreeRADIUS/
# chmod +x docker-entrypoint.sh
# docker build -t freeradius-otp:latest .
# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
freeradius-otp      latest              61b743094339        7 seconds ago       16.1MB
alpine              latest              a24bb4013296        8 weeks ago         5.57MB
```

#### 4. Create a Docker Image of Squid
```
# cd ../Squid
# docker build -t squid-otp:latest .
# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
squid-otp           latest              36ae18af19cb        14 seconds ago      19.4MB
freeradius-otp      latest              61b743094339        10 minutes ago      16.1MB
alpine              latest              a24bb4013296        8 weeks ago         5.57MB
```

#### 5. Start Containers
```
# cd ..
# docker-compose up -d
Creating network "squid-otp_default" with the default driver
Creating squid-otp_freeradius_1 ... done
Creating squid-otp_squid_1      ... done
# docker ps
CONTAINER ID        IMAGE                   COMMAND                  CREATED             STATUS              PORTS                              NAMES
08e2c3d14071        squid-otp:latest        "/usr/sbin/squid -NYC"   27 seconds ago      Up 26 seconds       0.0.0.0:8080->8080/tcp             squid-otp_squid_1
fb16a5678364        freeradius-otp:latest   "/docker-entrypoint.…"   27 seconds ago      Up 27 seconds       0.0.0.0:1812-1813->1812-1813/udp   squid-otp_freeradius_1
```

#### 6. Create new user
```
# adduser --disabled-password --gecos "" proxyuser
# su proxyuser
$ google-authenticator
$ exit
# docker-compose restart
```
