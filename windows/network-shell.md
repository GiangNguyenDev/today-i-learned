# Manage certificate

## Add SSL certificate

```console
netsh http add sslcert ipport=0.0.0.0:8895 certhash=%certhash% appid=%appid% clientcertnegotiation=enable
```

## Show SSL certificate

```console
netsh http show sslcert 0.0.0.0:8895
```

## Delete SSL certificate

```console
netsh http delete sslcert ipport=0.0.0.0:8895
```