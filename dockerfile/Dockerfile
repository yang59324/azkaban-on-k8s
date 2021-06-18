FROM openjdk:8u252-jre

MAINTAINER zang

# debian：buster(10)
RUN echo "deb http://mirrors.aliyun.com/debian/ buster main non-free contrib" > /etc/apt/sources.list \
 && echo "deb http://mirrors.aliyun.com/debian-security buster/updates main" >> /etc/apt/sources.list \
 && echo "deb http://mirrors.aliyun.com/debian/ buster-updates main non-free contrib" >> /etc/apt/sources.list 

RUN set -eux; \
	apt-get update; \
	apt-get install -y --no-install-recommends \
		procps \
        vim \
		mariadb-client \
		# python installation required
		gcc \
		libc6-dev \
		make \
	; \
	rm -rf /var/lib/apt/lists/*

# make and install python
RUN mkdir -p /opt/python_install
WORKDIR /opt/python_install

RUN wget https://www.python.org/ftp/python/2.7.18/Python-2.7.18.tgz
RUN tar -zxvf Python-2.7.18.tgz
RUN cd Python-2.7.18
RUN ./configure --prefix=/opt/python_install --enable-optimizations
RUN make
RUN make install
RUN chmod +x /opt/python_install/bin/python
RUN cp /opt/python_install/bin/python /bin/python

# azkaban
COPY . /tmp/

RUN mkdir -p /opt/azkaban \
 && tar zxf /tmp/azkaban-exec-server-3.84.4.tar.gz -C /opt/azkaban/ \
 && ln -s /opt/azkaban/azkaban-exec-server-3.84.4 /opt/azkaban/azkaban-exec \
 && tar zxf /tmp/azkaban-web-server-3.84.4.tar.gz -C /opt/azkaban/ \
 && ln -s /opt/azkaban/azkaban-web-server-3.84.4 /opt/azkaban/azkaban-web \
 && mv /tmp/start-azkaban.sh /usr/bin \
 && chmod a+x /usr/bin/start-azkaban.sh \
 && mv /tmp/active-exe.sh /usr/bin \
 && chmod a+x /usr/bin/active-exe.sh \
 && rm -rf /tmp/* 

WORKDIR /opt/azkaban/

ENV AZKABAN_EXE_HOME=/opt/azkaban/azkaban-exec \
    AZKABAN_WEB_HOME=/opt/azkaban/azkaban-web \
    PATH=${PATH}:${AZKABAN_EXE_HOME}/bin:${AZKABAN_WEB_HOME}/bin

ENTRYPOINT ["start-azkaban.sh"]
CMD [ "exe" ]