
# Run Bitnami OpenLdap

## Start OpenLdap container

docker run -p 127.0.0.1:1389:1389/tcp -it --rm --name openldap --env LDAP_ORGANISATION=example --env LDAP_DOMAIN=example.com --env LDAP_ROOT=dc=example,dc=com --env LDAP_ADMIN_USERNAME=admin --env LDAP_ADMIN_PASSWORD=adminpassword --env LDAP_CONFIG_ADMIN_ENABLED=yes --env LDAP_CONFIG_ADMIN_USERNAME=config --env LDAP_CONFIG_ADMIN_PASSWORD=configpassword --env LDAP_USERS=customuser --env LDAP_PASSWORDS=custompassword bitnami/openldap:2.6.4-debian-11-r32

docker run -p 127.0.0.1:1389:1389/tcp -it --rm --name openldap --env LDAP_ANISATION=example --env LDAP_DOMAIN=example.com --env LDAP_ROOT=dc=example,dc=com --env LDAP_ADMIN_USERNAME=admin --env LDAP_ADMIN_PASSWORD=adminpassword --env LDAP_CONFIG_ADMIN_ENABLED=yes --env LDAP_CONFIG_ADMIN_USERNAME=config --env LDAP_CONFIG_ADMIN_PASSWORD=configpassword --env LDAP_USERS=customuser --env LDAP_PASSWORDS=custompassword hykuo13104991/openldap:2.6.4-argon2

## Access OpenLdap container

docker exec -it -u root openldap bash

## Build Custom Image

docker build -t hykuo13104991/openldap:2.6.4-argon2 .

rm -rf /opt/bitnami/openldap/etc/slapd.d/*
slaptest -f /opt/bitnami/openldap/etc/slapd.conf -F /opt/bitnami/openldap/etc/slapd.d

## ldapsearch

ldapsearch -b "dc=example,dc=com" -H ldap://127.0.0.1:1389 -D "cn=admin,dc=example,dc=com" -x -W adminpassword
ldapsearch -b -H ldap://localhost:1389 "dc=example,dc=com" -D "cn=admin,dc=example,dc=com" -x -w secret
