# Overview

This repository contains a Dockerfile and some scripts which demonstrate a way in which you might run [IBM Integration Bus v9](http://www-03.ibm.com/software/products/en/ibm-integration-bus) in a [Docker](https://www.docker.com/whatisdocker/) container.

Please note that this docker file is still in early stages and might contain some unnecessary/extra commands. For now, this docker file is working.
I'm in the process of optimizing the the creation of the docker container.

# Building the image

The image can be built using standard [Docker commands](https://docs.docker.com/userguide/dockerimages/) against the supplied Dockerfile.  For example:

~~~
cd 9.0.0.1
docker build -t iibv9image .
~~~

This will create an image called iibv9image in your local docker registry.

# Run the IIB 9 Docker Image

~~~
docker run -it -p 1414:1414 -p 4414:4414 -p 7800:7800 iib9image:latest
~~~

# Run IIB 9
~~~
strmqm IIB9_QMGR_1
mqsistart IIB9_NODE_1
~~~

# Create IIB 9 Execution Groups
~~~
mqsicreateexecutiongroup IIB9_NODE_1 -e default
mqsicreateexecutiongroup IIB9_NODE_1 -e docker
~~~

# Stop/Start IIB 9
~~~
mqsistop -i IIB9_NODE_1
endmqm -i IIB9_QMGR_1

strmqm IIB9_QMGR_1
mqsistart IIB9_NODE_1
~~~