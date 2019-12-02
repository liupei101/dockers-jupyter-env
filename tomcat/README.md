## docker-tomcat9

Docker image of tomcat for publishing web applications.

## Usage

1. copy files
 
copy your `webapps` to the directory `./tomcat`.

2. docker build

```bash
docker build -t tomcat:app .
```

3. docker run

for testing the built image, use the following commands:
```bash
docker run \
    --name my-tomcat
    -p 8080:8080
    -d tomcat:app
```

for running your real apps, use the following commands:
```bash
docker run \
    --name my-tomcat \
    --restart always \
    --privileged=true \
    -p 8080:8080 \
    -v $(pwd)/tomcat/logs:/usr/local/tomcat/logs \
    -v $(pwd)/tomcat/conf/sys.properties:/usr/local/tomcat/webapps/ROOT/WEB-INF/classes/sys.properties \
    -v $(pwd)/tomcat/conf/database.properties:/usr/local/tomcat/webapps/ROOT/WEB-INF/classes/database.properties \
    -d tomcat:app
```

use `-v` to mount your log files and some configuration files.
