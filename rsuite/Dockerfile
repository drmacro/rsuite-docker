FROM isuper/java-oracle:jdk_7

MAINTAINER Eliot Kimber <ekimber@contrext.com>

ENV RSUITE_HOME=/opt/RSuite
COPY rsuite36 ${RSUITE_HOME}
COPY rsuite36/bin /etc/init.d
#RUN apt update && \
#    apt install net-tools
RUN chmod a+x /etc/init.d/rsuite-init.sh
RUN adduser --disabled-password --gecos '' rsuite && chown -R rsuite:rsuite ${RSUITE_HOME}

# Set environment variables that don't need to vary from time to time
ENV ANT_HOME=/opt/ant
ENV JDKPATH=$JAVA_HOME
ENV	PATH=$ANT_HOME/bin:$JAVA_HOME:$PATH

# Expose RSuite
EXPOSE 8080 8005

USER rsuite
CMD touch ${RSUITE_HOME}/logs/rsuite-server.log && \
    ${RSUITE_HOME}/tomcat/bin/catalina.sh start && \
    tail -f ${RSUITE_HOME}/logs/rsuite-server.log
#
# End of Dockerfile
#