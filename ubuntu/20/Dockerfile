FROM ubuntu:20.04
MAINTAINER Gerrit Code Review Community

# Add Gerrit packages repository
RUN apt-get update && \
    apt-get -y install gnupg2 python3
RUN echo "deb mirror://mirrorlist.gerritforge.com/bionic gerrit contrib" > /etc/apt/sources.list.d/GerritForge.list
RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 847005AE619067D5

RUN apt-get update
RUN apt-key update
RUN apt-get -y install sudo

ADD entrypoint.sh /

# Install OpenJDK and Gerrit in two subsequent transactions
# (pre-trans Gerrit script needs to have access to the Java command)
RUN apt-get -y install openjdk-11-jdk
RUN apt-get -y install gerrit=3.2.2-1 gitweb && \
    apt-mark hold gerrit && \
    /entrypoint.sh init && \
    rm -f /var/gerrit/etc/{ssh,secure}* && rm -Rf /var/gerrit/{static,index,logs,data,index,cache,git,db,tmp}/* && \
    mkdir -p /var/gerrit/hooks && \
    chown -R gerrit:gerrit /var/gerrit

ENV TZ=Asia/Shanghai

# add python for hooks scriopt
RUN apt install -y software-properties-common && \
    add-apt-repository ppa:deadsnakes/ppa && \
    apt update && \
    apt install -y python3.8 && \
    ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

USER gerrit

ENV CANONICAL_WEB_URL=
ENV HTTPD_LISTEN_URL=

# Allow incoming traffic
EXPOSE 29418 8080

VOLUME ["/var/gerrit/git", "/var/gerrit/index", "/var/gerrit/cache", "/var/gerrit/db", "/var/gerrit/etc", "/var/gerrit/logs", "/var/gerrit/hooks"]

ENTRYPOINT /entrypoint.sh
