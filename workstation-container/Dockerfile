FROM spark-base
MAINTAINER Tim Jones (doctimjone@github)

# sudo curl -sSL https://get.docker.com/ | sh
# sudo /etc/apparmor.d/docker
# sudo service apparmor reload
# docker build -t workstation-container .
# docker volume create --name dev

# interactive:
# docker run -i -t -p 9999:9999 -p 18888:8888 -v dev:/dev --name pyspark workstation-container

# docker run -d -v dev:/dev \
# -e "SPARK_HOME=/SparkSource/spark" \
# -e "JAVA_HOME=/usr/lib/jvm/java-8-oracle" \
# -e "PATH=$PATH:/SparkSource/spark/bin" \
# -e "PYTHONPATH=/SparkSource/spark/python:$PYTHONPATH" \
# -e "PYTHONPATH=/SparkSource/spark/python/lib/py4j-0.9-src.zip:$PYTHONPATH"
# --name pyspark workstation-container

# Debian packages
ENV DEBIAN_FRONTEND noninteractive
COPY packages.txt /usr/local/packages.txt
RUN apt-get update && cat /usr/local/packages.txt | xargs apt-get install -yq

# Pip packages
COPY ./requirements.txt /usr/local/requirements.txt
RUN pip3 --no-cache-dir install -r /usr/local/requirements.txt

# Install Cloud9 IDE
RUN ln -s /usr/bin/nodejs /usr/bin/node
RUN mkdir /usr/local/c9
WORKDIR /usr/local/c9
RUN git clone https://github.com/c9/core.git
WORKDIR /usr/local/c9/core
RUN scripts/install-sdk.sh
RUN curl -L https://raw.githubusercontent.com/c9/install/master/install.sh | bash

WORKDIR /root
RUN mkdir -p /var/log/supervisor
RUN mkdir -p /var/log/supervisor/conf.d
COPY ./c9.conf /etc/supervisor/conf.d/c9.conf
COPY ./jupyter.conf /etc/supervisor/conf.d/jupyter.conf

################
# https://github.com/jupyter/docker-stacks/blob/master/base-notebook/Dockerfile
################

# Install Tini
RUN wget --quiet https://github.com/krallin/tini/releases/download/v0.9.0/tini && \
    echo "faafbfb5b079303691a939a747d7f60591f2143164093727e870b289a44d9872 *tini" | sha256sum -c - && \
    mv tini /usr/local/bin/tini && \
    chmod +x /usr/local/bin/tini

# Configure environment
ENV CONDA_DIR /opt/conda
ENV PATH $CONDA_DIR/bin:$PATH
ENV SHELL /bin/bash
ENV NB_USER root
#ENV NB_UID 1000
#ENV HOME /home/$NB_USER
ENV LC_ALL en_US.UTF-8
ENV LANG en_US.UTF-8
ENV LANGUAGE en_US.UTF-8

# Install conda
RUN cd /tmp && \
    mkdir -p $CONDA_DIR && \
    wget --quiet https://repo.continuum.io/miniconda/Miniconda3-3.19.0-Linux-x86_64.sh && \
    echo "9ea57c0fdf481acf89d816184f969b04bc44dea27b258c4e86b1e3a25ff26aa0 *Miniconda3-3.19.0-Linux-x86_64.sh" | sha256sum -c - && \
    /bin/bash Miniconda3-3.19.0-Linux-x86_64.sh -f -b -p $CONDA_DIR && \
    rm Miniconda3-3.19.0-Linux-x86_64.sh && \
    $CONDA_DIR/bin/conda install --quiet --yes conda==3.19.1 && \
    $CONDA_DIR/bin/conda config --system --add channels conda-forge && \
    conda clean -tipsy

# Install Jupyter notebook
RUN conda install --quiet --yes \
    'notebook=4.2*' \
    && conda clean -tipsy

# Add local files as late as possible to avoid cache busting
COPY start-notebook.sh /usr/local/bin/
RUN chmod a+x /usr/local/bin/start-notebook.sh
RUN mkdir -p /root/.jupyter
COPY jupyter_notebook_config.py /root/.jupyter/

CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/supervisord.conf", "-n"]