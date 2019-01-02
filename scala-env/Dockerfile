FROM basic-env

ENV JAVA_OPTS="-Xms8192m -Xmx12288m -Xss2m"

USER root

RUN apt-get -y update && \
    apt-get -y install openjdk-8-jdk

RUN echo "deb https://dl.bintray.com/sbt/debian /" | tee -a /etc/apt/sources.list.d/sbt.list
RUN apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 2EE0EA64E40A89B84B2DF73499E82A75642AC823
RUN apt-get update -y && apt-get install -y sbt

USER $USERNAME
