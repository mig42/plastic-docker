FROM ubuntu:trusty

MAINTAINER Miguel González <mgonzalez@codicefactory.com>

RUN sudo apt-get update
RUN sudo apt-get install -y apt-transport-https

RUN mkdir /conf
RUN mkdir /logs
RUN mkdir -p /db/sqlite

RUN echo "deb https://www.plasticscm.com/plasticrepo/stable/ubuntu/ ./" | sudo tee /etc/apt/sources.list.d/plasticscm-stable.list
RUN wget https://www.plasticscm.com/plasticrepo/stable/ubuntu/Release.key -O - | sudo apt-key add -
RUN sudo apt-get update

RUN DEBIAN_FRONTEND=noninteractive apt-get -q update && apt-get install -y -q plasticscm-client plasticscm-server && plasticsd stop

RUN { \
    clconfigureserver --language=en --port=8087 --workingmode=UPWorkingMode; \
    [ -f /opt/plasticscm5/server/users.conf ] && mv /opt/plasticscm5/server/users.conf /conf || touch /conf/users.conf; \
    [ -f /opt/plasticscm5/server/groups.conf ] && mv /opt/plasticscm5/server/groups.conf /conf || touch /conf/groups.conf; \
    mv /opt/plasticscm5/server/plasticd.lic /conf; \
    ln -s /conf/users.conf /opt/plasticscm5/server && ln -s /conf/groups.conf /opt/plasticscm5/server; \
    ln -s /conf/plasticd.lic /opt/plasticscm5/server; \
    ln -s /conf/plasticd.token.lic /opt/plasticscm5/server; \
}

RUN umtool cu root root
RUN umtool cg administrators
RUN umtool autg root administrators

ADD loader.log.conf /opt/plasticscm5/server/
ADD db.conf /opt/plasticscm5/server/

EXPOSE 8087

VOLUME /conf
VOLUME /db/sqlite
VOLUME /logs

CMD ["/opt/plasticscm5/server/plasticd", "--daemon"]
