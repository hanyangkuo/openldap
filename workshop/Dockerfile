from bitnami/openldap:2.6.4-debian-11-r32

COPY /slapdldif/slapd.conf /opt/bitnami/openldap/etc

USER root
COPY /slapdldif/libopenldap.sh /opt/bitnami/scripts
RUN chmod 644 /opt/bitnami/scripts/libopenldap.sh
