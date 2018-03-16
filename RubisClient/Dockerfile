FROM centos:7

# install all required packages
RUN yum -y update \ 
    && yum install -y httpd \
                      wget \
                      make \
                      gnuplot \ 
                      openssh-server \
                      openssh-clients \
                      sysstat \
    && wget -q 'http://mirror.karneval.cz/pub/linux/fedora/epel/7/x86_64/Packages/e/epel-release-7-11.noarch.rpm' \
    && rpm -ivh epel-release-7-11.noarch.rpm \
    && yum install -y supervisor \
    && wget -q --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; \
       oraclelicense=accept-securebackup-cookie" \
       http://download.oracle.com/otn-pub/java/jdk/8u161-b12/2f38c3b165be4555a1fa6e98c45e0808/jdk-8u161-linux-x64.rpm \
    && yum install -y /jdk-8u161-linux-x64.rpm \
    && rm -f /jdk-8u161-linux-x64.rpm \
    && yum clean all \
    && rm -rf /var/cache/yum

#configure supervisord

RUN mkdir -p /var/log/supervisor
COPY ./supervisord.conf /etc/supervisor/conf.d/supervisord.conf
RUN echo "files = /etc/supervisor/conf.d/*.conf" >> /etc/supervisord.conf

#create ssh keys

RUN /usr/sbin/sshd-keygen 2>/dev/null
COPY ./ssh-keys/rubis_rsa_key /root/.ssh/id_rsa
COPY ./ssh-keys/rubis_rsa_key.pub root/.ssh/authorized_keys
RUN echo -e '\tStrictHostKeyChecking no'>>/etc/ssh/ssh_config \
    && echo -e '\tUserKnownHostsFile /dev/null'>>/etc/ssh/ssh_config


#Rubis Client installation
COPY Client /var/www/Client

RUN rm -f /var/www/html/index.html \ 
    && ln -s /var/www/Client/bench /var/www/html/bench

ENV RUBIS_HOME /var/www/Client
ENV TARGET benchmark

EXPOSE 80

CMD ["-c","/etc/supervisord.conf"]
ENTRYPOINT ["/usr/bin/supervisord"]
