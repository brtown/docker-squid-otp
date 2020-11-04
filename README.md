
#### 1. Pre-Installation
```
$ sudo -i
# apt update
# apt install -y vim git docker docker-compose libpam-google-authenticator libqrencode4
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
