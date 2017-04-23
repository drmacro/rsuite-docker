FROM patrickmcelwee/marklogic-dependencies:8-latest

MAINTAINER Eliot Kimber <ekimber@contrext.com>

ARG INSTALLER=MarkLogic-6.0-6.x86_64.rpm

COPY ${INSTALLER} /tmp/MarkLogic.rpm
COPY python /tmp/python

# Install python modules and MarkLogic
	
RUN python /tmp/python/setuptools/setup.py install && \
	cd /tmp/python/requests && \
	python setup.py install && \
	cd / && \
	yum -y install /tmp/MarkLogic.rpm && \
	rm -rf /tmp/*


# Expose MarkLogic admin
EXPOSE 7997 7998 7999 8000 8001 8002


# Define default command (which avoids immediate shutdown)
CMD /opt/MarkLogic/bin/MarkLogic && tail -f /dev/null