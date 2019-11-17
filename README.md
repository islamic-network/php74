[![CircleCI](https://circleci.com/gh/islamic-network/php74.svg?style=shield)](https://circleci.com/gh/islamic-network/php74)
[![](https://img.shields.io/github/license/islamic-network/php74.svg)](https://github.com/islamic-network/php74/blob/master/LICENSE.txt)
![Docker Pulls](https://img.shields.io/docker/pulls/islamicnetwork/php74)

# Work in Progress

# PHP 7.4 Docker Image with Apache

This repository produces a PHP 7.4 Image with Apache 2.4, ready for production use.

It is based on the official PHP 7.4 Docker Image and runs Debian.

It will work with OpenShift Online, Sloppy.io or any other Docker hosts.

The Apache document root in the container is /var/www/html and Apache is exposed on port 8080.

## Pull from Docker Hub to use

For production use, without XDebug:

```
docker run -d -p 8080:8080 -it --name=php -v $(pwd)/var/www/html:/var/www/html quay.io/islamic-network/php74:latest
```

or with XDebug:
```
docker run -d -p 8080:8080 -it --name=php -v $(pwd)/var/www/html:/var/www/html quay.io/islamic-network/php74:dev
```

## Build to use as a development environment (i.e., with XDebug):
```
docker build -f Dockerfile.dev . -t php74:dev
docker run -d -p 8080:8080 -it --name=php -v $(pwd)/var/www/html:/var/www/html php74:dev
```

## Build to use as a production environment:
```
docker build -f Dockerfile . -t php74
docker run -d -p 8080:8080 -it --name=php -v $(pwd)/var/www/html:/var/www/html php74
```


## Published Images
islamicnetwork/php74:latest (https://hub.docker.com/r/islamicnetwork/php74/). 

## Using Docker Secrets
This image now supports Docker Swarm Secrets via a modification of https://github.com/rbdiang/docker-secrets-example.

It can be tested in the exact same way using the Example in that repo.

## Important Information:

### Apache Settings

The container is setup by default to allow 150 Apache Request Workers. Knowing what this means is important when using this image for production applications.
You don't want to size your Docker container to where resources are wasted or you hit swap.
So, to tune this properly, for your running application, calculate the RAM needed for each request / process. Run the following where your app runs:
```
ps aux | grep 'apache2' | awk '{print $6/1024 " MB";}'
```

This will produce something like this:
```
18.1953 MB
15.2539 MB
15.4766 MB
15.3789 MB
15.3516 MB
15.3281 MB
15.3711 MB
15.3477 MB
0.6875 MB
```

This means your app is taking on average, 16-17 MB to serve a request. If you run WordPress or Drupal, this may be closer to 50 MB.

So, this would mean that to serve upto 150 requests, your container needs a maximum of 17 MB x 150 = 2550 + 100 MB opcache = 2650 MB of RAM.

Add another 200 MB as a safety net for any other processes. So, an image running this container should get no more than 2750 MB of RAM. Additional RAM will likely go unused.

Less RAM will result in usage of SWAP on disk if you hit more than 150 requests, which means it may take a few minutes for Apache to respond with extremely high CPU load.

If you expect more than 150 requests a second (or at one time) with a single instance, they will be queued. Alternatively, you can scale horizontally and deploy multiple instances.

You can also extend this container and overwrite these settings using your own Dockerfile. See, for instance, the Dockerfile in this project: https://github.com/islamic.network/alquran.cloud.

### PHP Modules and Extensions
 
The following modules / extensions / PECLs are enabled on this container (excluding XDebug which is only available in the :dev tag):

#### Extensions
* calendar
* iconv 
* bcmath 
* xml 
* gd 
* mbstring 
* pdo 
* tidy 
* gettext 
* intl 
* pdo 
* pdo_mysql 
* mysqli 
* simplexml 
* sockets
* tokenizer 
* xml 
* xmlwriter 
* zip

#### PECLs
* Redis
* GeoIP
* gRPC
* Memcached
* TimezoneDB
* APCu

## Need something else added?

Raise a pull request or an issue. 

## License
This program is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details.
