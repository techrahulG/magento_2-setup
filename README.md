# magento_2-setup
This repo having all the required details to stand up the magento2 setup.

please follow the all steps one by one according to Document.
# Dockerfile

Dockerfile start with FROM Tag which is allow to pull image
    FROM centos:7
RUN tag is use to run command inside the image you have pulled

    FROM centos:7
    MAINTAINER sarath.kandala@adapty.com
    RUN yum -y install epel-release
    RUN yum install nginx -y
    RUN yum install git -y
    RUN mkdir -p /var/www/html/preface
    RUN yum update -y
    RUN yum install http://rpms.remirepo.net/enterprise/remi-release-7.rpm -y
    RUN yum install yum-utils -y
    RUN yum-config-manager --enable remi-php72 -y
    RUN yum install -y \
    php \
    php-mcrypt \
    php-cli \
    php-gd \
    php-curl \
    php-mysql \
    php-ldap\
    php-zip \
    php-fileinfo \
    php-fpm \
    php-devel \
    php-mbstring \
    php-mcrypt \
    php-soap \
    php-apc \
    php-common \
    php-pear    
    RUN sed -i "s|cgi.fix_pathinfo=1|cgi.fix_pathinfo=0|g" /etc/php.ini
    RUN sed -i '/cgi.fix_pathinfo/s/^;//g' /etc/php.ini
    COPY default.conf  /usr/local/bin
    RUN sed -i '38,57 s/^/#/' /etc/nginx/nginx.conf
    COPY default.conf /etc/nginx/conf.d
    COPY bash.sh  /mnt
    RUN chmod +x /mnt/bash.sh
    RUN yum install php-bcmath -y
    RUN yum install php-intl -y

    CMD systemctl enable nginx.service
    CMD systemctl start nginx.service
    CMD systemctl enable php-fpm.service
    ENTRYPOINT ["/usr/sbin/init"]

