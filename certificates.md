# Certificates Cheat Sheet
## Process to create csr
https://medium.com/@rkakodker/how-to-simple-way-of-generating-wildcard-san-ssl-csrs-for-product-managers-8c25d715d86f

## common openssl commands
https://www.sslshopper.com/article-most-common-openssl-commands.html

## Generate private key file
```
openssl genrsa -des3 -out my_privkey.key 4096
```

## Generate Certificate Signing Request (CSR) for public key generation based on private key file.
```
openssl req -new -sha256 -out private.csr -key my_privkey.key  -config ssl.conf
```

## Validate details in a CSR file
```
openssl req -text -noout -verify -in private.csr
```

## Convert a PEM to DER command line
```
openssl x509 -outform der -in certificate.cer -out certificate.der
```

## Convert a P7B (with chain) to PEM
```
openssl pkcs7 -in ./cacert.p7b -inform DER -print_certs -out cacert.cer
```

## Convert an RSA (private key) to PEM
```
openssl rsa -in ./my_privkey.key -out ./private-tst.pem
```

## Convert an RSA (private key) to X509 (PKCS8)
```
openssl pkcs8 -topk8 -in private.pem -out pkcs8.private.pem -nocrypt
```

## Validate public, private and CSR (each should have the same sha256sum output)
```
openssl pkey -in my_privkey.key -pubout -outform pem | sha256sum
openssl pkey -in private-tst.pem -pubout -outform pem | sha256sum
openssl x509 -in kafka-tst.tst.events.network.dev.cer -pubkey -noout -outform pem | sha256sum
openssl req -in private.csr -pubkey -noout -outform pem | sha256sum
```
