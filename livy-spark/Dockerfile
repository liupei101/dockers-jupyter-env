FROM ubuntu:18.04
LABEL maintainer="@yuukilp"

# packages
RUN apt-get update && apt-get install -yq --no-install-recommends \
    wget \
    unzip \
    openjdk-8-jdk \
    python \
    python-pip

# install Anaconda3
ENV CONDA_VERSION 5.2.0
ENV CONDA_DIR /opt/anaconda3

RUN cd ~ && \
    wget https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/Anaconda3-${CONDA_VERSION}-Linux-x86_64.sh && \
    bash Anaconda3-${CONDA_VERSION}-Linux-x86_64.sh -f -b -p $CONDA_DIR && \
    rm -f Anaconda3-${CONDA_VERSION}-Linux-x86_64.sh

ENV PATH $CONDA_DIR/bin:$PATH
RUN conda clean -all -y

# pip configuration
RUN mkdir /root/.pip && \
    echo "[global]" > /root/.pip/pip.conf && \
    echo "index-url=http://mirrors.aliyun.com/pypi/simple/" >> /root/.pip/pip.conf && \
    echo "trusted-host=mirrors.aliyun.com" >> /root/.pip/pip.conf && \
    python -m pip install --upgrade pip

# Overall ENV vars
ENV SPARK_VERSION 2.4.4
ENV LIVY_VERSION 0.6.0-incubating

# Set install path for Livy
ENV LIVY_APP_PATH /apps/livy

# Set Hadoop config directory
ENV HADOOP_CONF_DIR /etc/hadoop/conf

# Set Spark home directory
ENV SPARK_HOME /usr/local/spark

# Spark ENV vars
ENV SPARK_VERSION_STRING spark-$SPARK_VERSION-bin-hadoop2.7
ENV SPARK_DOWNLOAD_URL http://mirrors.tuna.tsinghua.edu.cn/apache/spark/spark-$SPARK_VERSION/spark-$SPARK_VERSION-bin-hadoop2.7.tgz

# Livy ENV vars
ENV LIVY_VERSION_STRING apache-livy-$LIVY_VERSION-bin
ENV LIVY_DOWNLOAD_URL http://mirrors.tuna.tsinghua.edu.cn/apache/incubator/livy/$LIVY_VERSION/apache-livy-$LIVY_VERSION-bin.zip

# Download and unzip Spark
RUN wget $SPARK_DOWNLOAD_URL && \
    mkdir -p $SPARK_HOME && \
    tar xvf $SPARK_VERSION_STRING.tgz -C /tmp && \
    mv /tmp/$SPARK_VERSION_STRING/* $SPARK_HOME && \
    rm $SPARK_VERSION_STRING.tgz

# Download and unzip Livy
RUN wget $LIVY_DOWNLOAD_URL && \
    mkdir -p $LIVY_APP_PATH && \
    unzip $LIVY_VERSION_STRING.zip -d /tmp && \
    mv /tmp/$LIVY_VERSION_STRING/* $LIVY_APP_PATH && \
    rm $LIVY_VERSION_STRING.zip && \
    mkdir $LIVY_APP_PATH/logs

# Add custom files, set permissions
ADD entrypoint.sh .

RUN chmod +x entrypoint.sh

# Expose port
EXPOSE 8998

ENTRYPOINT ["/entrypoint.sh"]
