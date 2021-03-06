FROM php:7.4.13-fpm
MAINTAINER wuyihao <303440188@qq.com>

ARG CHANGE_SOURCE=true
ARG INSTALL_TIDEWAYS=false

# 替换源
COPY sources.list /etc/apt/china.sources.list
RUN if [ ${CHANGE_SOURCE} = true ]; then \
	mv /etc/apt/sources.list /etc/apt/source.list.bak && mv /etc/apt/china.sources.list /etc/apt/sources.list \
;fi


# 更新、安装基础组件
RUN apt-get update
RUN apt-get install -y  --allow-remove-essential --allow-downgrades --no-install-recommends \
    procps libpq-dev libfreetype6-dev \
    libjpeg62-turbo-dev libpng-dev  ntpdate \
    cron vim unzip git wget libzip-dev \
    libncurses5 libncursesw5 libtinfo5 libz-dev  zlib1g=1:1.2.8.dfsg-5



####################################################################################
# 安装 PHP 扩展
####################################################################################
RUN docker-php-ext-configure gd --with-freetype=/usr/include/ --with-jpeg=/usr/include/
RUN docker-php-ext-install pdo_mysql gettext bcmath

#################### 方式一

## 安装 tideways
#RUN if [ ${INSTALL_TIDEWAYS} = true ]; then \
#    echo 'deb http://s3-eu-west-1.amazonaws.com/qafoo-profiler/packages debian main' > /etc/apt/sources.list.d/tideways.list \
#    && wget -qO - https://s3-eu-west-1.amazonaws.com/qafoo-profiler/packages/EEB5E8F4.gpg | apt-key add - \
#    && apt-get install tideways-php tideways-daemon \
#    && cd /var/www \
#    && git clone https://github.com/laynefyc/xhgui-branch.git \
#    && cd /var/www/xhgui-branch \
#    && php install.php \
#;fi

RUN apt-get install pkg-config librabbitmq-dev
RUN pecl install amqp

# Swoole
COPY swoole-4.6.0.tgz /home/swoole.tgz
RUN pecl install /home/swoole.tgz \
		&& docker-php-ext-enable swoole

# Psr
#COPY psr-0.6.1.tgz /home/psr.tgz
#RUN pecl install /home/psr.tgz


#Phalcon
COPY phalcon3.2.4.so /usr/local/lib/php/extensions/no-debug-non-zts-20170718/phalcon.so


# Redis
COPY redis-4.0.0.tgz /home/redis.tgz
RUN pecl install /home/redis.tgz \
		&& docker-php-ext-enable redis



# Qconf
COPY qconf-1.2.1.tar.gz /home/qconf-1.2.1.tar.gz
COPY QConf-1.2.1_with_monitor-x86_64.tar.gz /home/QConf-1.2.1_with_monitor-x86_64.tar.gz
RUN tar -zxf /home/qconf-1.2.1.tar.gz && tar -zxf /home/QConf-1.2.1_with_monitor-x86_64.tar.gz -C /usr/local && mv /usr/local/qconf-1.2.1 /usr/local/Qconf \
        && cd /home/QConf-1.2.1/driver/php && phpize \
        && ./configure --with-php-config=/usr/local/bin/php-config --with-libqconf-dir=/usr/local/Qconf/include --enable-static LDFLAGS=/usr/local/Qconf/lib/libqconf.a \
        && make && make install \
		&& docker-php-ext-enable qconf



#mongodb
#RUN apt-get install openssl \
#    && apt-get update \
#    && apt-get install libssl1.0-dev \
#    && pecl install mongodb \
#    && docker-php-ext-enable mongodb




### php composer
RUN php -r "copy('https://install.phpcomposer.com/installer', 'composer-setup.php');" \
		&& php composer-setup.php \
		&& php -r "unlink('composer-setup.php');" \
		&& mv composer.phar /usr/local/bin/composer \
		&& composer config -g repo.packagist composer https://mirrors.aliyun.com/composer


ENV LC_ALL C.UTF-8
ENV ENV_PHP_VAR local


# Mongodb
#COPY mongodb-1.4.2.tgz /home/mongodb.tgz
#RUN pecl install /home/mongodb.tgz \
#		&& docker-php-ext-enable mongodb

# Memcached
#RUN apt-get install -y libmemcached-dev zlib1g-dev
#COPY memcached-3.0.4.tgz /home/memcached.tgz
#RUN pecl install /home/memcached.tgz \
#		&& docker-php-ext-enable memcached


#Include yii framework
#RUN echo 'include_path = "/var/www/php-framework/yii"' >> /usr/local/etc/php/php.ini



##################### 方式二 需要网络能访问 https://pecl.php.net/  ################################
# Memcached
#RUN apt-get install -y libmemcached-dev zlib1g-dev \
#		&& pecl install memcached \
#		&& docker-php-ext-enable memcached

#GeoIp
#RUN apt-get install -y libgeoip-dev \
#		&& pecl install geoip-1.1.1 \
#		&& docker-php-ext-enable geoip
#RUN mkdir -p /usr/share/GeoIP
#ADD GeoLiteCity.dat /usr/share/GeoIP/GeoIPCity.dat




