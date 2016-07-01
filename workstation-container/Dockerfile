FROM spark-base:latest
MAINTAINER Tim Jones (tdj28@github)

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

RUN mkdir -p /var/log/supervisor
RUN mkdir -p /var/log/supervisor/conf.d
COPY ./c9.conf /etc/supervisor/conf.d/c9.conf
COPY ./jupyter.conf /etc/supervisor/conf.d/jupyter.conf

# Add local files as late as possible to avoid cache busting
COPY start-notebook.sh /usr/local/bin/
COPY jupyter_notebook_config.py /root/.jupyter/

CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/supervisord.conf"]