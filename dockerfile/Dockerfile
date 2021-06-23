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
		zlib1g-dev libffi-dev libssl-dev \
	; \
	rm -rf /var/lib/apt/lists/*

COPY . /tmp/

# make and install python
RUN mkdir -p /opt/python_install
WORKDIR /opt/python_install

RUN tar -zxvf Python-3.6.13.tgz &&\
    cd Python-3.6.13 &&\
    ./configure --prefix=/opt/python_install &&\
    make &&\
    make install &&\
    chmod +x /opt/python_install/bin/python3.6 &&\
    cp /opt/python_install/bin/python3.6 /bin/python

RUN wget https://bootstrap.pypa.io/get-pip.py
RUN python get-pip.py
RUN python -m pip install --no-cache-dir -r /tmp/requirements.txt

# azkaban
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
