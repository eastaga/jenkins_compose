FROM ubuntu:16.04

# Install Docker CLI
RUN apt-get update && apt-get install -y apt-transport-https ca-certificates
RUN apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D
RUN echo "deb [arch=amd64] https://download.docker.com/linux/ubuntu xenial stable" > /etc/apt/sources.list.d/docker.list
RUN apt-get update && apt-get install -y docker-ce --allow-unauthenticated

RUN apt-get update && apt-get install -y openjdk-8-jdk curl python python-pip git
RUN easy_install jenkins-webapi

# Get docker-compose in the agent container

RUN curl -L https://github.com/docker/compose/releases/download/1.21.2/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose && chmod +x /usr/local/bin/docker-compose
RUN mkdir -p /home/jenkins
RUN mkdir -p /var/lib/jenkins

ADD slave.py /var/lib/jenkins/slave.py

WORKDIR /home/jenkins

ENV JENKINS_URL "http://jenkins"
ENV JENKINS_SLAVE_ADDRESS ""
ENV JENKINS_USER "admin"
ENV JENKINS_PASS "admin"
ENV SLAVE_NAME ""
ENV SLAVE_SECRET ""
ENV SLAVE_EXECUTORS "1"
ENV SLAVE_LABELS "docker"
ENV SLAVE_WORING_DIR ""
ENV CLEAN_WORKING_DIR "true"

#==========
# JDK
#=========

ENV JAVA_HOME /usr/lib/jvm/java-8-openjdk-amd64
ENV PATH "${PATH}:${JAVA_HOME}/bin"

#==========
# Maven
#==========
ENV MAVEN_VERSION 3.5.4

RUN curl -fsSL http://archive.apache.org/dist/maven/maven-3/$MAVEN_VERSION/binaries/apache-maven-$MAVEN_VERSION-bin.tar.gz | tar xzf - -C /usr/share \
  && mv /usr/share/apache-maven-$MAVEN_VERSION /usr/share/maven \
  && ln -s /usr/share/maven/apache-maven-$MAVEN_VERSION/bin/mvn /usr/bin/mvn

ENV MAVEN_HOME /usr/share/maven/apache-maven-$MAVEN_VERSION

#==========
#tomcat
#==========
RUN apt-get install -y wget 
RUN mkdir /usr/local/tomcat
RUN wget http://www-us.apache.org/dist/tomcat/tomcat-9/v9.0.12/bin/apache-tomcat-9.0.12.tar.gz -O /tmp/tomcat.tar.gz
RUN cd /tmp && tar xvfz tomcat.tar.gz
RUN cp -Rv /tmp/apache-tomcat-9.0.12/* /usr/local/tomcat/
RUN sed -i 's/port="8080"/port="8081"/' /usr/local/tomcat/conf/server.xml

#==========
# sonar scanner
#==========
RUN wget https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-3.2.0.1227-linux.zip -O /tmp/sonar-scanner-cli-3.2.0.1227-linux.zip
RUN apt-get install unzip
RUN cd /tmp && unzip sonar-scanner-cli-3.2.0.1227-linux.zip -d /usr/share/sonarscanner/
ENV PATH "${PATH}:/usr/share/sonarscanner/sonar-scanner-3.2.0.1227-linux/bin"



CMD [ "python", "-u", "/var/lib/jenkins/slave.py" ]
