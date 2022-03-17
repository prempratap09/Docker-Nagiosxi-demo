FROM ubuntu:20.04

RUN apt-get update 
RUN apt-get install -y curl
RUN apt-get install -y wget tar

WORKDIR /tmp


RUN wget https://assets.nagios.com/downloads/nagiosxi/xi-latest.tar.gz
RUN tar -zxf xi-latest.tar.gz

WORKDIR /tmp/nagiosxi

ADD config.cfg xi-sys.cfg
ADD post-install ./subcomponents/nagioscore/post-install
RUN ./init.sh \
    && . ./xi-sys.cfg \
	&& umask 0022 \
	&& . ./functions.sh \
	&& log="install.log"
RUN export INTERACTIVE="False" \
    && export INSTALL_PATH=`pwd`
RUN . ./functions.sh \
    && run_sub ./0-repos noupdate
RUN . ./functions.sh \
    && run_sub ./1-prereqs
RUN . ./functions.sh \
    && run_sub ./2-usersgroups
RUN . ./functions.sh \
    && run_sub ./3-dbservers
RUN . ./functions.sh \
    && run_sub ./4-services
RUN . ./functions.sh \
    && run_sub ./5-sudoers
RUN sed -i.bak s/selinux/sudoers/g 9-dbbackups
RUN . ./functions.sh \
    && run_sub ./9-dbbackups
RUN . ./functions.sh \
    && run_sub ./11-sourceguardian
RUN . ./functions.sh \
    && run_sub ./13-phpini

ADD scripts/NDOUTILS-POST subcomponents/ndoutils/post-install
ADD scripts/install subcomponents/ndoutils/install
RUN chmod 755 subcomponents/ndoutils/post-install \
    && chmod 755 subcomponents/ndoutils/install \
	&& . ./functions.sh \
	&& run_sub ./A-subcomponents \
	&& run_sub ./A0-mrtg

RUN service mysqld start \
    && . ./functions.sh \
	&& run_sub ./B-installxi
RUN . ./functions.sh \
    && run_sub ./C-cronjobs
RUN . ./functions.sh \
    && run_sub ./D-chkconfigalldaemons
RUN service mysqld start \
    && . ./functions.sh \
	&& run_sub ./E-importnagiosql
RUN . ./functions.sh \
    && run_sub ./F-startdaemons
RUN . ./functions.sh \
    && run_sub ./Z-webroot

RUN yum clean all

# set startup script
ADD start.sh /start.sh
RUN chmod 755 /start.sh
EXPOSE 80 5666 5667

CMD ["/start.sh"]


