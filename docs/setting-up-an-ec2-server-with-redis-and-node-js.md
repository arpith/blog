
# Setting up an EC2 server with Redis and Node.js

The API for feedreader.co (api.feedreader.co) is an Express app running on EC2. Here’s how I set it up.

![](https://cdn-images-1.medium.com/max/8192/1*MKlzDDoWVfpqBVZQ57nfbA.jpeg)

### Launch your instance

1. Click “Launch Instance”

1. Choose “Amazon Linux AMI”

1. Hit “Review and Launch”

Don’t forget to download your key pair!

### Install Node.js and Redis

SSH into your instance, and run sudo yum update. Then install [screen](https://en.wikipedia.org/wiki/GNU_Screen) and run screen -DRR. Now you can proceed to install [node](https://nodejs.org/en/download/current/):

    $ wget [https://nodejs.org/dist/v6.2.2/node-v6.2.2-linux-x64.tar.xz](https://nodejs.org/dist/v6.2.2/node-v6.2.2-linux-x64.tar.xz)
    $ tar -C /usr/local --strip-components 1 -xJf node-v6.2.2-linux-x64.tar.xz

And [Redis](http://redis.io/download):

    $ wget http://download.redis.io/releases/redis-3.2.1.tar.gz
    $ tar xzf redis-3.2.1.tar.gz
    $ cd redis-3.2.1 && make

### Run your app

In my case this involves grabbing the dump.rdb file for Redis from S3, cloning the app’s github repository (https://github.com/feedreaderco/api) and running npm install & npm start.

Starting redis:

    $ cd ~/redis-3.2.1
    $ curl [https://s3.amazonaws.com/your-s3-bucket-name/dump.rdb](https://s3.amazonaws.com/feedreader2016-redis-seed/dump.rdb)
    $ src/redis-server

And installing and starting the app:

    $ git clone [https://github.com/feedreaderco/api](https://github.com/feedreaderco/api)
    $ cd api
    $ npm install && npm start

That’s it!
