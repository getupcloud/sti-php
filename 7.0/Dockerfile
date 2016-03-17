FROM openshift/base-centos7

# This image provides an Apache+PHP environment for running PHP
# applications.

MAINTAINER SoftwareCollections.org <sclorg@redhat.com>

EXPOSE 8080

ENV PHP_VERSION=7.0

LABEL io.k8s.description="Platform for building and running PHP 7.0 applications" \
      io.k8s.display-name="Apache 2.4 with PHP 7.0" \
      io.openshift.expose-services="8080:http" \
      io.openshift.tags="builder,php,php70,rh-php70"

# Install Apache httpd and PHP
RUN yum install -y epel-release https://mirror.webtatic.com/yum/el7/webtatic-release.rpm && \
    INSTALL_PKGS="php70w php70w-opcache php70w-bcmath php70w-cli php70w-common php70w-dba php70w-devel \
                 php70w-embedded php70w-enchant php70w-fpm php70w-gd php70w-imap php70w-interbase php70w-intl php70w-ldap \
                 php70w-mbstring php70w-mcrypt php70w-mysqlnd php70w-odbc  php70w-opcache php70w-pdo \
                 php70w-pdo_dblib php70w-pear php70w-pecl-apcu php70w-pecl-imagick php70w-pecl-xdebug php70w-pgsql php70w-phpdbg \
                 php70w-process php70w-pspell php70w-recode php70w-snmp php70w-soap php70w-tidy php70w-xml php70w-xmlrpc" && \
    yum install -y --setopt=tsflags=nodocs $INSTALL_PKGS && \
    rpm -V $INSTALL_PKGS && \
    yum clean all -y

# Copy the S2I scripts from the specific language image to $STI_SCRIPTS_PATH
COPY ./s2i/bin/ $STI_SCRIPTS_PATH

# Each language image can have 'contrib' a directory with extra files needed to
# run and build the applications.
COPY ./contrib/ /opt/app-root

# In order to drop the root user, we have to make some directories world
# writeable as OpenShift default security model is to run the container under
# random UID.
RUN sed -i -f /opt/app-root/etc/httpdconf.sed /etc/httpd/conf/httpd.conf && \
    sed -i '/php_value session.save_path/d' /etc/httpd/conf.d/php.conf && \
    head -n151 /etc/httpd/conf/httpd.conf | tail -n1 | grep "AllowOverride All" || exit && \
    mkdir /tmp/sessions && \
    chmod -R a+rwx /var/run/httpd && \
    chmod -R a+rwx /tmp/sessions && \
    chown -R 1001:0 /opt/app-root /tmp/sessions /etc/php.ini /etc/php.d

USER 1001

# Set the default CMD to print the usage of the language image
CMD $STI_SCRIPTS_PATH/usage
