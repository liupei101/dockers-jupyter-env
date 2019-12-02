# docker-livy

A Docker image for [Apache Livy, a REST Service for Apache Spark](https://livy.apache.org/).

This repo is forked from [tobilg/docker-livy](https://github.com/tobilg/docker-livy). But some changes have made to it for more convience to chinese users:
- use `tsinghua` mirrors for quick docker building 
- install `anaconda` and `python3.6` for pyspark applications
- use `aliyun` repo for pip

## Build
Build the docker images using commands as follows:
```bash
docker build -t my-livy-spark .
```

## Running

The image can be run with

`docker run -p 8998:8998 -d my-livy-spark`

which will expose the port `8998` on the Docker host for this image.

## Details

Have a look at the [official docs](https://livy.apache.org/docs/latest/rest-api.html) to see how to use the Livy REST API.
