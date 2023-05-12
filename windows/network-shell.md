# Manage certificate

## Show SSL certificate

```console
netsh http show sslcert

netsh http show sslcert ipport=0.0.0.0:8891

netsh http show sslcert hostnameport=qa.zurich-rechner.de:443
```

## Set Variables for Certificate and Application Id

```console
SET certhash=424a2f5b69be78f3b6508640c94aab479d7420f2
SET appid={00112233-4455-6677-8899-AABBCCDDEEFF}
```

## Bind SSL Certificate to IP Address and Port

```console
netsh http add sslcert ipport=0.0.0.0:8891 certhash=%certhash% appid=%appid% clientcertnegotiation=enable
```

## Bind SSL Certificate to Hostname and Port

```console
netsh http add sslcert hostnameport=service.local:8891 certhash=%certhash% appid=%appid% clientcertnegotiation=enable certstorename=MY
```

## Update SSL Certificate

```console
netsh http update sslcert hostnameport=service.local:8891 certhash=%certhash% appid=%appid% clientcertnegotiation=enable
```

## Delete SSL certificate

```console
netsh http delete sslcert hostnameport=service.local:8891
```
