# Manage certificate

## Show SSL certificate

```console
netsh http show sslcert

netsh http show sslcert ipport=0.0.0.0:8891

netsh http show sslcert hostnameport=qa.zurich-rechner.de:443
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
