FROM ubuntu:14.04
MAINTAINER Eliot Kimber <ekimber@contrext.com>


ENV PATH /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/opt/MarkLogic/mlcmd/bin
ENV LD_LIBRARY_PATH /opt/MarkLogic/lib

ARG INSTALLER=marklogic_4.2-10.1_amd64.deb

COPY ${INSTALLER} /tmp/MarkLogic.deb

# Install lsb-base and MarkLogic:
    	
RUN apt-get update && \
    apt-get -y install lsb-base && \
    dpkg -i /tmp/MarkLogic.deb && \
    rm /tmp/MarkLogic.deb


# Expose MarkLogic admin
EXPOSE 7997 7998 7999 8000 8001 8002 8040 8041 8042


# Define default command (which avoids immediate shutdown)
CMD /opt/MarkLogic/bin/MarkLogic && tail -f /dev/null