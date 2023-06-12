
# Run Bitnami OpenLdap

## Start OpenLdap container

docker run -p 127.0.0.1:1389:1389/tcp -it --rm --name openldap --env LDAP_ORGANISATION=example --env LDAP_DOMAIN=example.com --env LDAP_ROOT=dc=example,dc=com --env LDAP_ADMIN_USERNAME=admin --env LDAP_ADMIN_PASSWORD=adminpassword --env LDAP_CONFIG_ADMIN_ENABLED=yes --env LDAP_CONFIG_ADMIN_USERNAME=config --env LDAP_CONFIG_ADMIN_PASSWORD=configpassword --env LDAP_USERS=customuser --env LDAP_PASSWORDS=custompassword bitnami/openldap:2.6.4-debian-11-r32

docker run -p 127.0.0.1:1389:1389/tcp -it --rm --name openldap --env LDAP_ANISATION=example --env LDAP_DOMAIN=example.com --env LDAP_ROOT=dc=example,dc=com --env LDAP_ADMIN_USERNAME=admin --env LDAP_ADMIN_PASSWORD=adminpassword --env LDAP_CONFIG_ADMIN_ENABLED=yes --env LDAP_CONFIG_ADMIN_USERNAME=config --env LDAP_CONFIG_ADMIN_PASSWORD=configpassword --env LDAP_USERS=customuser --env LDAP_PASSWORDS=custompassword hykuo13104991/openldap:2.6.4-argon2

## Access OpenLdap container

Check image entrypoint `docker inspect --format='{{.Config.Cmd}}' hykuo13104991/openldap:2.6.4-argon2`
Check image entrypoint `docker inspect --format='{{.Config.Cmd}}' bitnami/openldap:2.6.4-debian-11-r32`
docker exec -it -u root openldap bash

## Build Custom Image

docker build -t hykuo13104991/openldap:2.6.4-debian-11-r36 .

rm -rf /opt/bitnami/openldap/etc/slapd.d/*
slaptest -f slapd.conf -F /opt/bitnami/openldap/etc/slapd.d
slaptest -f slapd.conf -F /bitnami/openldap/slapd.d

## ldapsearch

ldapsearch -b "dc=example,dc=com" -H ldap://127.0.0.1:1389 -D "cn=admin,dc=example,dc=com" -x -W adminpassword
ldapsearch -b -H ldap://localhost:1389 "dc=example,dc=com" -D "cn=admin,dc=example,dc=com" -x -w secret
ldappasswd -H ldap://127.0.0.1:1389 -D "cn=admin,dc=example,dc=com" -x -W -S cn=customuser,ou=users,dc=example,dc=com

https://linux.die.net/man/5/slapd-config

## ldap tool

ldapadd
ldapdelete
ldapmodify
ldappasswd
ldapurl
ldapwhoami
ldapcompare
ldapexop
ldapmodrdn
ldapsearch
ldapvc

## Command

### Password Hash Using slappasswd

`slappasswd -h {ARGON2} -s test -o module-load=argon2`

### Test Argon2

1. Create adam.ldif

```sh
cat > "adam.ldif" << EOF
dn: cn=adam,ou=users,dc=example,dc=com
cn: adam
objectClass: person
sn: adam
userPassword: {ARGON2}\$argon2i\$v=19\$m=4096,t=3,p=1\$J77rVOLnNfJC6vLrb0AJpg\$PmpGFAO8mKJRNyuqyBU5EQKjLTawUeon7w8wokIPRYw
EOF
```

1. Create adam using ldif

`ldapadd -x -W -D "cn=admin,dc=example,dc=com" -f adam.ldif -H ldap://127.0.0.1:1389`
adminpassword

1. Test user/password with ldapsearch

`ldapsearch -b "dc=example,dc=com" -H ldap://127.0.0.1:1389 -D "cn=adam,ou=users,dc=example,dc=com" -x -w secret`

### Change Password

`ldappasswd -s welcome123 -W -D "cn=admin,dc=example,dc=com" -x "cn=adam,ou=users,dc=example,dc=com" -H ldap://127.0.0.1:1389`
adminpassword

```sh
cat > "overlady-ppolicy.ldif" << EOF
dn: olcOverlay={2}ppolicy,olcDatabase=mdb,cn=config
objectClass: olcOverlayConfig
objectClass: olcPPolicyConfig
olcOverlay: {2}ppolicy
olcPPolicyHashCleartext: TRUE
EOF
```

ldapadd -Q -Y EXTERNAL -H ldapi:/// -f overlady-ppolicy.ldif
