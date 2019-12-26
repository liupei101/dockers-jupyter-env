FROM centos:7
LABEL maintainer="yuukilp"


## 1. Update yum repo to aliyun 
RUN cd /etc/yum.repos.d && \
    mv CentOS-Base.repo CentOS-Base.repo.backup && \
    curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo && \
    yum update -y


## 2. Enable zh_CN and Fix localtime
RUN rm -rf /etc/localtime && \ 
    ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime && \ 
    yum -y install kde-l10n-Chinese && \ 
    yum -y reinstall glibc-common && \ 
    localedef -c -f UTF-8 -i zh_CN zh_CN.utf8 && \
    yum clean all && rm -rf /var/cache/yum


## 3. Jupyter Env
ENV CONDA_VERSION=5.2.0 \ 
    CONDA_DIR=/opt/anaconda3

# 3.1 Install Anaconda3 and Add it to PATH
RUN yum install -y wget bzip2 && \
    cd ~ && \
    wget https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/Anaconda3-${CONDA_VERSION}-Linux-x86_64.sh && \
    bash Anaconda3-${CONDA_VERSION}-Linux-x86_64.sh -f -b -p $CONDA_DIR && \
    rm -f Anaconda3-${CONDA_VERSION}-Linux-x86_64.sh && \
    echo "export PATH=/opt/anaconda3/bin:$PATH" >> /root/.bashrc && \
    source /root/.bashrc && \
    conda clean --all -y && \
    yum clean all && rm -rf /var/cache/yum

# 3.2 Configure pip repo
ENV PATH $CONDA_DIR/bin:$PATH
RUN mkdir /root/.pip && \
    echo -e "[global]\nindex-url=http://mirrors.aliyun.com/pypi/simple/\ntrusted-host=mirrors.aliyun.com" > /root/.pip/pip.conf && \
    pip install --upgrade pip

# 3.3 Install notedown for MarkDown in Jupyter and Upgrade notebook package
RUN pip install msgpack notedown notebook==6.0.2 && \
    rm -rf /root/.cache/pip


## 4. Install R + PySpark Kernel
# 4.1 R denpendency
RUN yum install -y epel-release && \
    yum install -y R && \
    yum install -y czmq-devel libcurl-devel openssl-devel && \
    yum clean all && rm -rf /var/cache/yum

# 4.2 R kernel
RUN echo 'options(repos=structure(c(CRAN="https://mirrors.tuna.tsinghua.edu.cn/CRAN/")))' > /root/.Rprofile && \
    Rscript -e 'install.packages(c("repr", "IRdisplay", "IRkernel"), type = "source")' && \
    Rscript -e 'IRkernel::installspec(user = FALSE)'

# 4.3 PySpark kernel and configuration (Livy + Spark services are on port 8998)
ENV PYTHON_VERSION=3.6

RUN pip install sparkmagic && \
    jupyter nbextension enable --py --sys-prefix widgetsnbextension && \
    jupyter-kernelspec install ${CONDA_DIR}/lib/python${PYTHON_VERSION}/site-packages/sparkmagic/kernels/pysparkkernel && \
    jupyter serverextension enable --py sparkmagic && \
    jupyter nbextension disable --py widgetsnbextension && \
    rm -rf /root/.cache/pip

## 5. Install ssh for remote login
RUN yum install -y net-tools && \
    yum install -y openssl openssh-server && \
    yum clean all && rm -rf /var/cache/yum && \
    ssh-keygen -q -t rsa -b 2048 -f /etc/ssh/ssh_host_rsa_key -N '' && \
    ssh-keygen -q -t ecdsa -f /etc/ssh/ssh_host_ecdsa_key -N '' && \
    ssh-keygen -t dsa -f /etc/ssh/ssh_host_ed25519_key -N '' && \
    echo "root:mopu123" | chpasswd

ADD ./conf/sshd_config /etc/ssh/sshd_config

EXPOSE 22


## 6. EntryPoint
ADD ./start.sh .

RUN chmod +x start.sh

ENTRYPOINT ["/start.sh"]
