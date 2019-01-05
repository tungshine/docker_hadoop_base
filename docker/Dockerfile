# build a new hadoop image with basic centos
FROM centos
# who is the author
MAINTAINER alex <362615233@qq.com>


# install some important softwares and make a new directory to story jdk and hadoop files
RUN yum -y install openssh-server openssh-clients net-tools which vim \
    && mkdir -p /soft/


# copy the jdk and hadoop  archive to the image, and it will automaticlly unzip the tar file
ADD jdk-8u101-linux-x64.tar.gz /soft/
ADD hadoop-2.7.5.tar.gz /soft/

RUN ln -s /soft/jdk1.8.0_101/ /soft/jdk \
    && ln -s /soft/hadoop-2.7.5/ /soft/hadoop \
    && ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
    && echo "Asia/Shanghai" > /etc/timezone


############## Configurate SSH #################
# generate ssh key files and login localhost without password
RUN ssh-keygen -q -t rsa -b 2048 -f /etc/ssh/ssh_host_rsa_key -N '' \
    && ssh-keygen -q -t ecdsa -f /etc/ssh/ssh_host_ecdsa_key -N '' \
    && ssh-keygen -q -t dsa -f /etc/ssh/ssh_host_ed25519_key -N '' \
    && ssh-keygen -f /root/.ssh/id_rsa -N '' \
    && cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys

# copy the configuration file to image
COPY config/core-site.xml config/hdfs-site.xml config/slaves /soft/hadoop/etc/hadoop/

# change hadoop environment variables
RUN sed -i "s?JAVA_HOME=\${JAVA_HOME}?JAVA_HOME=/soft/jdk?g" /soft/hadoop/etc/hadoop/hadoop-env.sh


############## Integration configuration #################
# set environment variables
ENV JAVA_HOME /soft/jdk
ENV JRE_HOME ${JAVA_HOME}/jre
ENV CLASSPATH .:${JAVA_HOME}/lib;${JRE_HOME}/lib
ENV HADOOP_HOME /soft/hadoop
ENV PATH ${HADOOP_HOME}/bin:${HADOOP_HOME}/sbin:${JAVA_HOME}/bin:$PATH

# set password of root
RUN echo "root:" | chpasswd

# when start a container it will be executed
EXPOSE 22
CMD ["/usr/sbin/sshd", "-D"]

