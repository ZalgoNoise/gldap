# gldap

![CI](https://github.com/ZalgoNoise/gldap/workflows/CI/badge.svg)

__________




### Google LDAP Deployment and Querying tools

A simple and light Docker image for querying G Suite domains via Secure LDAP.

Google G Suite LDAP queries can be performed with OpenLDAP and STunnel, which are the sole tenants of this image.

The purpose is to be able to query and test your LDAP queries, in an easy and quick way - without ever leaving crums behind.


### Environment Definition

Deploying in an ephemeral approach is the preferred way, as STunnel takes little to no time launching. It is encouraged to keep the clean-up-after-yourself flag [`--rm`].

The private `.crt/.key` combination that you've downloaded should be moved into the `keys` folder and renamed `stunnel` as such:

```bash
/path/to/repo/keys/stunnel.crt
/path/to/repo/keys/stunnel.key
```

Which you can do after you've downloaded them. Unzip the archive into the `keys` folder, `cd` into it and run the commands:

```bash
cp *.crt stunnel.crt
cp *.key stunnel.key
```

The `keys` folder is the folder attached to the container holding the certificates. It should be linked to the `/data` folder in the container.

Alternatively, you can mount these files individually, renaming them inside the container to:
- `/data/stunnel.crt`
- `/data/stunnel.key`

Finally define the following environment variables when starting the container:

```
LDAP_USER=username
LDAP_PASS=password
LDAP_BASESEARCH=dc=ldaptest,dc=eu
```

### Container Deployment

Either export, source or replace the variables from the command below to perform a query:

```bash
docker run --rm -ti \
    --name gldap \
    -v `pwd`/keys:/data \
    -e LDAP_USER=$LDAP_USER \
    -e LDAP_PASS=$LDAP_PASS \
    -e LDAP_BASESEARCH=$LDAP_BASESEARCH \
    zalgonoise/gldap:latest \
    $LDAP_FILTER
```

You can also use `--env-file` and populate the provided template:

```bash
docker run --rm -ti \
    --name gldap \
    -v `pwd`/keys:/data \
    --env-file `pwd`/secrets.env \
    zalgonoise/gldap:latest
```

Lastly, the containter supports the `unzip` action, so you can provide the `.zip` file directly:

```bash
docker run --rm -ti \
    --name gldap \
    -v ~/Downloads/Google_2023_02_04_48611.zip:/data/Google.zip \
    --env-file `pwd`/secrets.env \
    zalgonoise/gldap:latest

```


If you prefer to add the .crt/.key combination individually:

```bash
docker run --rm -ti \
    --name gldap \
    -v `pwd`/keys/Google_20YY_MM_DD_XXXXX.crt:/data/stunnel.crt:ro \
    -v `pwd`/keys/Google_20YY_MM_DD_XXXXX.key:/data/stunnel.key:ro \
    -e LDAP_USER=$LDAP_USER \
    -e LDAP_PASS=$LDAP_PASS \
    -e LDAP_BASESEARCH=$LDAP_BASESEARCH \
    zalgonoise/gldap:latest \
    $LDAP_FILTER
```


You will be returned an output of the STunnel service, followed by the result of your LDAP query.

### Debug Mode

Running queries in OpenLDAP debug mode (by attaching the flag `-d5` to the `ldapsearch` command) can be done in through the `LDAP_FILTER` environment variable or provided as the first argument to the container:


```bash
docker run --rm -ti \
    --name gldap \
    -v `pwd`/keys/Google_20YY_MM_DD_XXXXX.crt:/data/stunnel.crt:ro \
    -v `pwd`/keys/Google_20YY_MM_DD_XXXXX.key:/data/stunnel.key:ro \
    -e LDAP_USER=$LDAP_USER \
    -e LDAP_PASS=$LDAP_PASS \
    -e LDAP_BASESEARCH=$LDAP_BASESEARCH \
    zalgonoise/gldap:latest \
    -d5 "objectClass=*"
```

This action will add the `-d5` flag to `ldapsearch`, dumping the debug output for your query.



~ ZalgoNoise ~ 2020 ~ 
