# Use latest openjdk:8-alpine image as the base
FROM openjdk:8-alpine

# Set the WILDFLY_VERSION env variable
ENV WILDFLY_VERSION 13.0.0.Final
ENV WILDFLY_SHA1 3d63b72d9479fea0e3462264dd2250ccd96435f9
ENV JBOSS_HOME /opt/jboss/wildfly

USER root

RUN apk update \
    && apk add --virtual .build-deps \
        curl \
        shadow \
    && mkdir -p /opt/jboss \
    && groupadd -r jboss -g 1000 && useradd -u 1000 -r -g jboss -m -d /opt/jboss -s /sbin/nologin -c "JBoss user" jboss \
    && chmod 755 /opt/jboss \
    && cd $HOME \
    && curl -O https://download.jboss.org/wildfly/$WILDFLY_VERSION/wildfly-$WILDFLY_VERSION.tar.gz \
    && sha1sum wildfly-$WILDFLY_VERSION.tar.gz | grep $WILDFLY_SHA1 \
    && tar xf wildfly-$WILDFLY_VERSION.tar.gz \
    && mv $HOME/wildfly-$WILDFLY_VERSION $JBOSS_HOME \
    && rm wildfly-$WILDFLY_VERSION.tar.gz \
    && chown -R jboss:0 ${JBOSS_HOME} \
    && chmod -R g+rw ${JBOSS_HOME} \
    && cd /root \
    && rm -rf .build-deps \
    && rm -rf /var/cache/apk/*

# Ensure signals are forwarded to the JVM process correctly for graceful shutdown
ENV LAUNCH_JBOSS_IN_BACKGROUND true

# Set the working directory to jboss' user home directory
WORKDIR /opt/jboss
# Specify the user which should be used to execute all commands below
USER jboss

# Expose the ports we're interested in
EXPOSE 8080

# Set the default command to run on boot
# This will boot WildFly in the standalone mode and bind to all interface
CMD ["/opt/jboss/wildfly/bin/standalone.sh", "-b", "0.0.0.0"]
