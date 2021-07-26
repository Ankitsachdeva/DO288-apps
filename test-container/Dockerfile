FROM   registry.access.redhat.com/ubi8/ubi:8.0

MAINTAINER   Red Hat Training <training@redhat.com>

# command line options to pass to the JVM
ENV	  JAVA_OPTIONS -Xmx512m

ARG CERTNAME=cert.pem
ARG ALIAS=test-group-root-ca

# Install the Java runtime, create a user for running the app, and set permissions
RUN   yum install -y --disableplugin=subscription-manager java-1.8.0-openjdk-headless && \
      yum clean all --disableplugin=subscription-manager -y && \
      useradd wildfly && \
      mkdir -p /opt/app-root/bin


USER root

# update both the OpenSSL and JDK CA trusts
RUN trust anchor --store /tmp/$CERTNAME \
  && update-ca-trust \
  && keytool -cacerts -importcert -alias $ALIAS -noprompt -storepass:env STOREPASS -file /tmp/$CERTNAME \
  && rm /tmp/$CERTNAME

# Copy the runnable fat JAR to the container.
ADD   https://github.com/RedHatTraining/DO288-apps/releases/download/OCP-4.1-1/hello-java.jar /opt/app-root/bin/

COPY  run-app.sh /opt/app-root/bin/

COPY cert.pem /tmp
RUN   chown -R wildfly:wildfly /opt/app-root && \
      chmod -R 700 /opt/app-root

EXPOSE 8080

USER  wildfly

# Run the fat JAR
CMD   /opt/app-root/bin/run-app.sh



