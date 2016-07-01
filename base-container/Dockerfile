FROM ubuntu:14.04
MAINTAINER Tim Jones (tdj28@github)

# sudo curl -sSL https://get.docker.com/ | sh
# sudo /etc/apparmor.d/docker
# sudo service apparmor reload
# docker build -t spark-base .
# docker volume create --name dev

# interactive:
# docker run -i -t -p 9999:9999 -p 18888:8888 -v dev:/dev --name pyspark spark-base

# docker run -d -v dev:/dev \
# -e "SPARK_HOME=/SparkSource/spark" \
# -e "JAVA_HOME=/usr/lib/jvm/java-8-oracle" \
# -e "PATH=$PATH:/SparkSource/spark/bin" \
# -e "PYTHONPATH=/SparkSource/spark/python:$PYTHONPATH" \
# -e "PYTHONPATH=/SparkSource/spark/python/lib/py4j-0.9-src.zip:$PYTHONPATH"
# --name pyspark spark-base

# Debian packages
ENV DEBIAN_FRONTEND noninteractive
COPY packages.txt /usr/local/packages.txt
RUN apt-get update && cat /usr/local/packages.txt | xargs apt-get install -yq

# Pip packages
WORKDIR /usr/local/ 
RUN curl "https://bootstrap.pypa.io/get-pip.py" -o "get-pip.py"
RUN python3 get-pip.py
COPY ./requirements.txt /usr/local/requirements.txt
RUN pip3 --no-cache-dir install -r /usr/local/requirements.txt

# Install Oracle's Java
RUN apt-add-repository ppa:webupd8team/java
RUN echo "oracle-java8-installer shared/accepted-oracle-license-v1-1 select true" | sudo debconf-set-selections
RUN apt-get update && apt-get install -yq \
    oracle-java8-installer \
    && apt-get clean && \
    rm -rf /var/lib/apt/lists/*

RUN locale-gen en_US en_US.UTF-8
RUN DEBIAN_FRONTEND=noninteractive dpkg-reconfigure locales

# Install Maven
ENV MAVEN_VERSION 3.3.9

RUN mkdir -p /usr/share/maven \
  && curl -fsSL http://apache.osuosl.org/maven/maven-3/$MAVEN_VERSION/binaries/apache-maven-$MAVEN_VERSION-bin.tar.gz \
    | tar -xzC /usr/share/maven --strip-components=1 \
  && ln -s /usr/share/maven/bin/mvn /usr/bin/mvn

ENV MAVEN_HOME /usr/share/maven

# Get Spark Source
RUN mkdir /SparkSource
WORKDIR /SparkSource
RUN wget http://mirror.cc.columbia.edu/pub/software/apache/spark/spark-1.6.1/spark-1.6.1.tgz && tar -xvf spark-1.6.1.tgz && ln -is spark-1.6.1 spark
WORKDIR /SparkSource/spark
ENV MAVEN_OPTS "-Xmx2g -XX:ReservedCodeCacheSize=512m"
RUN mvn -Psparkr,yarn,hadoop-2.3,parquet-provided -DskipTests -Djline.terminal=jline.UnsupportedTerminal clean package

ENV SPARK_HOME "/SparkSource/spark" 
ENV JAVA_HOME "/usr/lib/jvm/java-8-oracle"
ENV PATH "/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/SparkSource/spark/bin"
ENV PYTHONPATH "/SparkSource/spark/python:/SparkSource/spark/python/lib/py4j-0.9-src.zip"

