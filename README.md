# magento_2-setup
This repo having all the required details to stand up the magento2 setup.

please follow the all steps one by one according to Document.
# Dockerfile

Dockerfile start with FROM Tag which is allow to pull image
    FROM centos:7
RUN tag is use to run command inside the image you have pulled

    FROM centos:7
    MAINTAINER your_email@gmail.com
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
    RUN yum install php-bcmath -y
    RUN yum install php-intl -y
here it is updating cgi.fix_pathinfo=1 to cgi.fix_pathinfo=0 and also uncommenting it.
    
    RUN sed -i "s|cgi.fix_pathinfo=1|cgi.fix_pathinfo=0|g" /etc/php.ini
    RUN sed -i '/cgi.fix_pathinfo/s/^;//g' /etc/php.ini
    
you have to copy your default.conf file inside containers /etc/nginx/conf.d

      COPY default.conf /etc/nginx/conf.d
    
This  RUN command will comment the lines from 38 to 57 in nginx.conf inside docker image.

    RUN sed -i '38,57 s/^/#/' /etc/nginx/nginx.conf
    
    
Here it is copping the default.conf file into /mnt folder
    COPY bash.sh  /mnt
    RUN chmod +x /mnt/bash.sh

    
ervice will start automatically but we need to enable it.

    CMD systemctl enable nginx.service
    CMD systemctl start nginx.service
    CMD systemctl enable php-fpm.service
    ENTRYPOINT ["/usr/sbin/init"]



# docker-compose.yml

version is very important part of this file which is depend on docker version.

    version: "3.3"

inside services you can define project title.

     services:
         magento:
  
here Dockerfile is getting called   

      build: .  

after building Dockerfile this will tag image with name 

    image: 'rahuldock16/magento2'    
    
set the container name 

      container_name: 'magento_2'        
      
allow you to make changes in prodution environment also

       environment:
          PRODUCTION: 'true'    
      
port tag is use to expose ports

      ports:
        - "60010:80"
      
 volume tag is use to mount the volumes from inside container to outside
      
      volumes:
         - /devops/rahul/project-name/data:/var/www/html/project-name
         - /devops/rahul/project-name/nginx:/etc/nginx/conf.d
         - /devops/rahul/project-name/logs:/var/log/nginx
         - ./bash.sh:/mnt/bash.sh
privileged tag is for grant permission to container.

      privileged: true

Now here will run bash file.
  
     command: bash -c "sh /mnt/bash.sh"
      
At the end you need to  define the volumes
      
      networks:
        my-network:
      volumes:
        container-volume:
  
 # bash.sh
 
     #!/bin/bash
    git clone git_path_to_clone -v /var/www/html/project_name
    
# default.conf

    upstream fastcgi_backend {
    server 127.0.0.1:9000;
        }
    server {
    listen 80;
    server_name _;
    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;
    #return 404;
    set $MAGE_ROOT /var/www/html/project_file_namae;
    include /var/www/html/project_file_namae/nginx.conf.sample;
    }


# After this there are some manual step related to database connection.

    bin/magento setup:install --base-url=weburl --db-host=host_id --db-name=db_name --db-user=database_user --db-password=database_password --admin-firstname=firstname --admin-lastname=sirname --admin-email=your_mail_id@gmail.com --admin-user=admin_name --admin-password=password --backend-frontname=admin --language=en_US --currency=KWD --cleanup-database --use-rewrites=1
    
Once you have done with all steps kindly restart the services of nginx and php-fpm
   
    systemctl restart nginx
    systemctl restart php-fpm

 
 
 
        

        
