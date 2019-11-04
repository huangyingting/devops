# We deploy with ubuntu so that devs have a familiar environment.
FROM ubuntu:18.04

RUN \
 apt-get update && \
 apt-get install apt-transport-https wget curl software-properties-common -y && \
 wget -q https://packages.microsoft.com/config/ubuntu/18.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb && \
 dpkg -i packages-microsoft-prod.deb && \
 rm packages-microsoft-prod.deb && \
 curl -sL https://deb.nodesource.com/setup_12.x | bash - && \
 apt-get update && \
 apt-get install -y \
	openssl \
	net-tools \
	iproute2 \
	tcpdump \
	iputils-ping \
	git \
	locales \
	sudo \
	dumb-init \
	vim \
	build-essential \
	gdb \
	golang-go \
	python3 \
	nodejs \
	luajit \
	lua5.3 \
	python \
	python-pip \
	python3-pip \
	dotnet-sdk-3.0 \
	sudo && \
 apt-get remove software-properties-common apt-transport-https -y && \
 apt-get autoremove -y && \
 mkdir -p /tmp && \
 if [ -z ${CODE_RELEASE+x} ]; then \
	CODE_RELEASE=$(curl -sX GET "https://api.github.com/repos/cdr/code-server/releases/latest" \
	| awk '/tag_name/{print $4;exit}' FS='[""]'); \
 fi && \
 curl -o \
 /tmp/code.tar.gz -L \
	"https://github.com/cdr/code-server/releases/download/${CODE_RELEASE}/code-server${CODE_RELEASE}-linux-x86_64.tar.gz" && \
 tar xzf /tmp/code.tar.gz -C \
	/usr/local/bin/ --strip-components=1 \
  --wildcards code-server*/code-server && \
 rm -rf \
	/tmp/* \
	/var/lib/apt/lists/* \
	/var/tmp/*

RUN locale-gen en_US.UTF-8
# We cannot use update-locale because docker will not use the env variables
# configured in /etc/default/locale so we need to set it manually.
ENV LC_ALL=en_US.UTF-8 \
	SHELL=/bin/bash

RUN adduser --gecos '' --disabled-password coder && \
	echo "coder ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers.d/nopasswd

USER coder

EXPOSE 8080

ENTRYPOINT ["dumb-init", "code-server", "--host", "0.0.0.0"]
