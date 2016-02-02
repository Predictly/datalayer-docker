# Licensed to Datalayer (http://datalayer.io) under one or more
# contributor license agreements.  See the NOTICE file
# distributed with this work for additional information
# regarding copyright ownership. Datalayer licenses this file
# to you under the Apache License, Version 2.0 (the
# "License"); you may not use this file except in compliance
# with the License.  You may obtain a copy of the License at
#
#   http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing,
# software distributed under the License is distributed on an
# "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
# KIND, either express or implied.  See the License for the
# specific language governing permissions and limitations
# under the License.

FROM centos:6

MAINTAINER Datalayer <docker@datalayer.io>

########## Tools  ############

RUN rpm -ivh http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
RUN yum -y install wget dnsmasq unzip curl wget dnsmasq unzip curl vim sudo openssl telnet vim openssh-server openssh-clients sudo openssl telnet curl ntp tar openssl-devel telnet curl git htop

########## SSH  ############

RUN echo 'root:datalayer' | chpasswd
RUN ssh-keygen -q -t rsa -N '' -f /root/.ssh/id_rsa
RUN cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
RUN chmod 700 ~/.ssh
RUN chmod 600 ~/.ssh/authorized_keys

RUN groupadd hadoop

########## Java #################

RUN yum install -y java-1.7.0-openjdk-devel

ENV JAVA_HOME /usr/lib/jvm/java-1.7.0-openjdk-1.7.0.71.x86_64

ADD jce/README.txt /usr/lib/jvm/java-1.7.0-openjdk.x86_64/jre/lib/security/README.txt
ADD jce/local_policy.jar /usr/lib/jvm/java-1.7.0-openjdk.x86_64/jre/lib/security/local_policy.jar
ADD jce/US_export_policy.jar /usr/lib/jvm/java-1.7.0-openjdk.x86_64/jre/lib/security/US_export_policy.jar

ENV JAVA_HOME /usr/lib/jvm/java-1.7.0-openjdk.x86_64

########## Python ##########

RUN yum install -y python-pip numpy scipy python-matplotlib ipython python-pandas sympy python-nose
RUN pip install py4j pattern pandasql
# RUN pip install scikit-learn

########## R ##########

RUN yum install -y R R-devel

########## Kerberos ###########

RUN yum install -y krb5-server krb5-libs krb5-auth-dialog krb5-workstation

ADD etc/krb5.conf /etc/krb5.conf
ADD etc/kdc.conf /var/kerberos/krb5kdc/kdc.conf
ADD etc/kadm5.acl /var/kerberos/krb5kdc/kadm5.acl

RUN mkdir /etc/security/keytabs
RUN chown root:hadoop /etc/security/keytabs
RUN chmod 750 /etc/security/keytabs

RUN mkdir /var/log/kerberos/

ADD keytab/datalayer.keytab /etc/security/keytabs/
RUN chmod 440 /etc/security/keytabs/datalayer.keytab

RUN /usr/sbin/kdb5_util create -r DATALAYER.IO -P root -s

RUN /usr/sbin/kadmin.local -q "addprinc -randkey datalayer/docker.datalayer.io@DATALAYER.IO"

########## OpenLdap ##########

RUN yum -y install perl
RUN yum -y install openldap openldap-servers openldap-clients openldap-clients nss-pam-ldapd pam_ldap nscd autofs rpcbind nfs-utils authconfig

########## Ambari ##############

RUN wget -nv http://public-repo-1.hortonworks.com/ambari/centos6/2.x/updates/2.1.2/ambari.repo -O /etc/yum.repos.d/ambari.repo
RUN yum install -y ambari-server ambari-agent ambari-log4j

########## Startup Scripts ##########

ADD scripts/setup-ldap.sh /etc/setup-ldap.sh
RUN chmod 755 /etc/setup-ldap.sh

ADD resources/datalayer-cli-colors.sh /datalayer-cli-colors.sh
ADD resources/datalayer-echo-header.sh /datalayer-echo-header.sh
ADD scripts/start-services.sh /start-services.sh
RUN chmod 755 /start-services.sh

########## Ports ##########

EXPOSE 22
EXPOSE 80
EXPOSE 88
EXPOSE 123
EXPOSE 389
EXPOSE 443
EXPOSE 464
EXPOSE 636
EXPOSE 749
EXPOSE 750
EXPOSE 2181
EXPOSE 6080
EXPOSE 8000
EXPOSE 8020
EXPOSE 8025
EXPOSE 8030
EXPOSE 8042
EXPOSE 8050
EXPOSE 8080
EXPOSE 8088
EXPOSE 8090
EXPOSE 8141
EXPOSE 8188
EXPOSE 8190
EXPOSE 8440
EXPOSE 8441
EXPOSE 8443
EXPOSE 8671
EXPOSE 10020
EXPOSE 10200
EXPOSE 11000
EXPOSE 15000
EXPOSE 16010
EXPOSE 19888
EXPOSE 50070
EXPOSE 50090
EXPOSE 50470
EXPOSE 55188

########## Command #########

CMD ["/bin/bash"]

########## Entry Point ######

ENTRYPOINT ["/bin/bash", "/start-services.sh"]