# Install dependencies
FROM ubuntu:16.04
# Clean and update
RUN apt-get clean && apt-get update
RUN apt-get -y install curl && \
    apt-get -y install wget && \
    apt-get -y install apt-utils && \
    apt-get autoremove -y
# Node app
FROM node:carbon
# The base node image sets a very verbose log level.
ENV NPM_CONFIG_LOGLEVEL warn
# Add package.json to cache the file
ADD frontend/package.json /tmp/frontend/
ADD api/package.json /tmp/api/
# Copy package json files for services
COPY frontend/package.json /srv/www/frontend/
COPY api/package.json /srv/www/api/
# Set working dir
WORKDIR /srv/www
# Bundle app source
COPY . ./
# Install app dependencies
# To mitigate issues with npm saturating the network interface we limit the number of concurrent connections
RUN npm config set maxsockets 5 && npm config set progress false
RUN cd /srv/www/frontend && npm install && npm run build
RUN cd /srv/www/api && npm install && npm run build
# Install pm2
RUN npm install -g pm2
# Actual script to start can be overridden from `docker run`
CMD ["pm2", "start", "process.yml", "--no-daemon"]
# Expose ports
EXPOSE 5000
EXPOSE 8080
