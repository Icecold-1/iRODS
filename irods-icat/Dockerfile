FROM ubuntu:18.04

RUN apt-get -y update && apt-get -y upgrade 
RUN apt-get -y install wget curl nano sudo gnupg2 lsb-release netcat pylint python3 python3-pip python python-pip
RUN pip install queuelib dweepy hashlib configparser
RUN wget -qO - https://packages.irods.org/irods-signing-key.asc | sudo apt-key add - 
RUN echo "deb [arch=amd64] https://packages.irods.org/apt/ $(lsb_release -sc) main" | sudo tee /etc/apt/sources.list.d/renci-irods.list
RUN apt-get update -y
RUN apt-get -y install irods-server irods-database-plugin-postgres irods-dev \
 irods-resource-plugin-s3 \
 && useradd -d /var/lib/irods irods

RUN apt-get clean all

## installing irods python rule-engine plugin
RUN apt-get -y install irods-rule-engine-plugin-python

## environment variables for container runtime
ENV IRODS_ADMIN_USER=irods
ENV IRODS_ADMIN_PASS=test123
ENV IRODS_ZONE_NAME=tempZone
ENV IRODS_ZONE_KEY=TEMPORARY_zone_key
ENV IRODS_ZONE_PORT=1247
ENV IRODS_NEGOTIATION_KEY=TEMPORARY_32byte_negotiation_key
ENV IRODS_ICAT_DBSERVER=icat-db
ENV IRODS_ICAT_DBPORT=5432
ENV IRODS_ICAT_DBNAME=ICAT
ENV IRODS_ICAT_DBUSER=irods
ENV IRODS_ICAT_DBPASS=test123
ENV IRODS_CONTROLPLANE_PORT=1248
ENV IRODS_CONTROLPLANE_KEY=TEMPORARY__32byte_ctrl_plane_key
ENV IRODS_DATA_PORT_RANGE_BEG=20000
ENV IRODS_DATA_PORT_RANGE_END=20199

## copy scripts
COPY genresp.sh docker-entrypoint.sh irods_login.sh /
RUN chmod +x /genresp.sh /docker-entrypoint.sh /irods_login.sh /

## data volumes
VOLUME "/etc/irods" "/var/lib/irods/iRODS/server/log" "/var/lib/irods/Vault"

## set up B2HANDLE

WORKDIR /

RUN git clone https://github.com/EUDAT-B2SAFE/B2HANDLE
RUN git clone https://github.com/EUDAT-B2SAFE/B2SAFE-core

WORKDIR /B2HANDLE

RUN python setup.py install

## set up B2SAFE

WORKDIR /B2SAFE-core/packaging

RUN chmod +x /create_deb_package.sh

WORKDIR /

RUN dpkg -i /debbuild/irods-eudat-b2safe_4.2.0.deb



## network ports
EXPOSE 4321 $IRODS_ZONE_PORT $IRODS_CONTROLPLANE_PORT $IRODS_DATA_PORT_RANGE_BEG-$IRODS_DATA_PORT_RANGE_END

## entrypoint
ENTRYPOINT ["/docker-entrypoint.sh"]
CMD ["irods"]