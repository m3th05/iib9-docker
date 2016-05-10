FROM centos:7

MAINTAINER Johann Engelbrecht johann@methos.co.za

# Install curl
RUN yum update -y && \
yum install -y libgcc_s.so.1 ld-linux.so.2 gtk2 iptables net-tools

# COPY IIB 9 RUNTIME
RUN mkdir /opt/ibm
COPY 9.0.0-IIB-LINUXX64-DEVELOPER-RUNTIME.tar.gz /tmp
RUN tar -xvzf /tmp/9.0.0-IIB-LINUXX64-DEVELOPER-RUNTIME.tar.gz --directory /tmp

# Create Users
RUN useradd iibuser
RUN groupadd -r mqbrkrs
RUN usermod -a -G mqbrkrs iibuser

# Install MQ 7.5
WORKDIR /tmp/messagebroker_ia_developer/WebSphere_MQ_V7.5.0.1
RUN ./mqlicense.sh -accept -text_only

RUN rpm -ivh MQSeriesRuntime-*.x86_64.rpm && \
rpm -ivh MQSeriesServer-*.x86_64.rpm && \
rpm -ivh MQSeriesXRClients-*.x86_64.rpm && \
rpm -ivh MQSeriesClient-*.x86_64.rpm && \
rpm -ivh MQSeriesAMS-*.x86_64.rpm  && \
rpm -ivh MQSeriesJRE-*.x86_64.rpm  && \
rpm -ivh MQSeriesSDK-*.x86_64.rpm  && \
rpm -ivh MQSeriesJava-*.x86_64.rpm  && \
rpm -ivh MQSeriesGSKit-*.x86_64.rpm  && \
rpm -ivh MQSeriesSamples-*.x86_64.rpm && \
rpm -ivh MQSeriesMan-*.x86_64.rpm && \
rpm -ivh MQSeriesMsg_*.x86_64.rpm  && \
rpm -ivh MQSeriesExplorer-*.x86_64.rpm

RUN usermod -a -G mqm iibuser

RUN sed -i '$a source /opt/mqm/bin/setmqenv -s' /home/iibuser/.bash_profile
RUN sed -i '$a source /opt/mqm/bin/setmqenv -s' /home/iibuser/.bashrc

# Install IIB 9
WORKDIR /tmp/messagebroker_ia_developer
RUN ./setuplinuxx64 -i silent -DLICENSE_ACCEPTED=TRUE -DUSER_INSTALL_DIR=/opt/ibm/mqsi/9.0

# Install Integration Explorer
WORKDIR /tmp/messagebroker_ia_developer/IBExplorer
RUN chmod 775 install.bin

RUN ./install.bin -i silent -DLICENSE_ACCEPTED=TRUE

RUN sed -i '$a source /opt/ibm/mqsi/9.0/bin/mqsiprofile' /home/iibuser/.bash_profile
RUN sed -i '$a source /opt/ibm/mqsi/9.0/bin/mqsiprofile' /home/iibuser/.bashrc

# Change User
RUN whoami
USER iibuser
RUN whoami

# Create MQ Q Manager
RUN /opt/mqm/bin/crtmqm IIB9_QMGR_1

# Set MQ Q Manager ENV
RUN source /opt/mqm/bin/setmqenv -s

# Create IIB9 Node
RUN source /opt/ibm/mqsi/9.0/bin/mqsiprofile && \
/opt/ibm/mqsi/9.0/bin/mqsicreatebroker IIB9_NODE_1 -q IIB9_QMGR_1

WORKDIR /home/iibuser

# Open ports
EXPOSE 1414
EXPOSE 4414
EXPOSE 7800