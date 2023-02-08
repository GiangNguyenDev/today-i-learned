# Introduction

Self-signed certificate is useful for development and testing locally or with internal environment. It also reduces the cost/effort because you don't have to buy expensive certificate and maintain it. In this article, you will learn how to create and use a self-signed certificate with OpenSSL tool.

# Prerequisites

OpenSSL is installed (inclusive in GIT installation) and added into Environment Variable PATH (e.g., `C:\Program Files\Git\usr\bin\`). 

Create a file `domains.ext` that lists all your local domains and save into working directory. E.g.:

```console
authorityKeyIdentifier=keyid,issuer
basicConstraints=CA:FALSE
keyUsage = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment
subjectAltName = @alt_names
[alt_names]
DNS.1 = localhost
DNS.2 = 127.0.0.1
DNS.3 = my-login.local
DNS.4 = my-web-app.local
```

# Generate single Root Certificate

Go to working directory, where the `domains.ext` is stored. Run this command below.

```console
openssl req -x509 -nodes -new -sha256 -days 3650 -newkey rsa:2048 -keyout localdomain.CA.key -out localdomain.CA.pem -subj "/C=MyCountryCode/CN=localdomain.CA"
openssl x509 -outform pem -in localdomain.CA.pem -out localdomain.CA.crt
```

# Generate SSL Certificate

```console
openssl req -new -nodes -newkey rsa:2048 -keyout localdomain.key -out localdomain.csr -subj "/C=MyCountryCode/ST=MyState/L=MyCity/O=MyOrganisation/CN=localdomain"
openssl x509 -req -sha256 -days 3650 -in localdomain.csr -CA localdomain.CA.pem -CAkey localdomain.CA.key -CAcreateserial -extfile domains.ext -out localdomain.crt
```

# Create private Certificate for Web Hosting

```console
openssl pkcs12 -export -out localdomain.pfx -inkey localdomain.key -in localdomain.crt -certfile localdomain.CA.crt -name localdomain
```

# (Optional) Create key store file for Java Application

```console
keytool.exe -import -trustcacerts -file "localdomain.crt" -keystore localdomain_keystore.jks
```

# Add Root Certificate to Trusted Root CA

Open `Manage computer certificates` program and import the Root Certificate into `Trusted Root Certification Authorities` folder.

# Import SSL Certificate in IIS for Web Hosting

- Open IIS
- Open Feature `Server Certificates`
- Import the PFX file and enter the password. Select the Certificate Store `Web Hosting`
- Add HTTPS Binding with the new Certificate for local Web App