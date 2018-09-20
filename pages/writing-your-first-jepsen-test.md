
# Writing your first Jepsen test

Jepsen is a great library that lets you test distributed systems. I’m building a distributed database at the Recurse Center, and eventually want to run Jepsen on it. Here’s how you write a simple Jepsen test that does nothing!

### Create an EC2 instance

1. Sign up for [AWS](http://aws.amazon.com)

1. In Services -> EC2, click “Launch Instance”

1. Choose the 64 bit Debian Jessie image

1. Hit review and launch

Save your SSH key pair!

### Install Java

SSH into your instance and open /etc/apt/sources.list in your favorite editor. Add jessie-backports to the file:

    deb http://ftp.debian.org/debian jessie-backports main

Now run sudo apt-get update, and install the following!

    $ sudo apt-get install openjdk-8-jre openjdk-8-jre-headless libjna-java

### Use Docker

You need to [install docker](https://docs.docker.com/engine/installation/linux/debian/) and [docker compose](https://docs.docker.com/compose/install/). Then [spin up the containers](https://github.com/aphyr/jepsen/blob/master/docker/README.md):

    $ git clone [https://github.com/aphyr/jepsen](https://github.com/aphyr/jepsen) && cd jepsen/docker
    $ ./up.sh
    $ docker exec -it jepsen-control bash

### Write your test

Create a new Leiningen project:

    $ lein new foo && cd foo

And edit src/jepsen/foo.clj — your first test! This does nothing!

    (ns jepsen.zookeeper
      (:require [jepsen.tests :as tests]))
    
    (defn zk-test
      [version]
      tests/noop-test)

Edit test/jepsen/foo_test.clj to look like this:

    (ns jepsen.foo-test
      (:require [clojure.test :refer :all]
        [jepsen.core :as jepsen]
        [jepsen.foo :as foo]))

    (deftest a-test
      (is (:valid? (:results (jepsen/run! (foo/foo "3.4.5+dfsg-2"))))))

That calls the test you just wrote (and passes it a version number).

Finally! Run your test:

    $ lein test

And hopefully you get something that ends with:

    Everything looks good! ヽ(‘ー`)ノ

That’s it! Special thanks to [James](http://twitter.com/porterjamesj)!

Hiring? [Get](http://twitter.com/arpith) [in touch](mailto:arpith@feedreader.co)!
