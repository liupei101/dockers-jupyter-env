FROM centos:7

################################################# 7 ################################################
# Update yum repo to aliyun 
RUN cd /etc/yum.repos.d && \
    mv CentOS-Base.repo CentOS-Base.repo.backup && \
    curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo && \
    yum update -y

################################################# pure ################################################
# Enable zh_CN and Fix localtime
RUN rm -rf /etc/localtime && \ 
    ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime && \ 
    yum -y install kde-l10n-Chinese && \ 
    yum -y reinstall glibc-common && \ 
    localedef -c -f UTF-8 -i zh_CN zh_CN.utf8 && \ 
    yum clean all && rm -rf /var/cache/yum

################################################# base ################################################
# Jupyter Env
ENV CONDA_VERSION 5.2.0
ENV CONDA_DIR /opt/anaconda3

RUN yum install -y wget && \
    cd ~ && \
    wget https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/Anaconda3-${CONDA_VERSION}-Linux-x86_64.sh && \
    bash Anaconda3-${CONDA_VERSION}-Linux-x86_64.sh -f -b -p $CONDA_DIR && \
    rm -f Anaconda3-${CONDA_VERSION}-Linux-x86_64.sh

ENV PATH $CONDA_DIR/bin:$PATH

RUN conda clean --all -f -y && \
    yum clean all && rm -rf /var/cache/yum

# Configure pip repo
RUN mkdir /root/.pip && \
    echo -e "[global]\nindex-url=http://mirrors.aliyun.com/pypi/simple/\ntrusted-host=mirrors.aliyun.com" > /root/.pip/pip.conf


# Install notedown
RUN pip install notedown && \
    rm -rf /root/.cache/pip


# Add essential files for customized Jupyter
RUN mkdir /root/.jupyter && \
    mkdir /root/.jupyter/custom && \
    mkdir /home/notebooks

# COPY ./config/jupyter/jupyter_notebook_config.py /root/.jupyter/jupyter_notebook_config.py
# COPY ./config/jupyter/custom/custom.js /root/.jupyter/custom/custom.js
# COPY ./config/jupyter/custom/custom.css /root/.jupyter/custom/custom.css


################################################# stable ################################################
# Install R + PySpark Kernel
# R denpendency
RUN yum install -y epel-release && \
    yum install -y R && \
    yum install -y czmq-devel libcurl-devel openssl-devel && \
    yum clean all && rm -rf /var/cache/yum

# R kernel
RUN echo 'options(repos=structure(c(CRAN="https://mirrors.tuna.tsinghua.edu.cn/CRAN/")))' > /root/.Rprofile && \
    Rscript -e 'install.packages(c("repr", "IRdisplay", "IRkernel"), type = "source")' && \
    Rscript -e 'IRkernel::installspec(user = FALSE)'

# PySpark kernel (Livy + Spark services are on port 8998)
RUN pip install sparkmagic && \
    jupyter nbextension enable --py --sys-prefix widgetsnbextension && \
    jupyter-kernelspec install /opt/anaconda3/lib/python3.7/site-packages/sparkmagic/kernels/pysparkkernel && \
    rm -rf /root/.cache/pip

# PySpark configure
# COPY ./config/pyspark/config.json /root/.sparkmagic/config.json
RUN mkdir /root/.magic && \
    jupyter serverextension enable --py sparkmagic && \
    jupyter nbextension disable --py widgetsnbextension

################################################## pro #################################################