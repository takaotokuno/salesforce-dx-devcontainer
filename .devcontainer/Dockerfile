FROM mcr.microsoft.com/devcontainers/javascript-node:22

RUN apt-get update \
	&& apt-get install -y --no-install-recommends socat \
	&& rm -rf /var/lib/apt/lists/*

RUN npm install --global @salesforce/cli
