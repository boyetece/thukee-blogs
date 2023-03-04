# OpenSSL - Self-Signed Certificate

##  Definition:
PEM - Privacy Enhanced Mail, is a Base64-encoded certificate file used to authenticate a secure website. It may contain a private key, certificate authority (CA) server certificate, or other various certificates that make up the trust chain. PEM files are typically imported from a Unix-based Apache Web server and compatible with OpenSSL applications.

## Generate Private Key using RSA encryption
```
openssl genrsa -aes256 -out ca-key.pem 4096
```
## Generate a Private CA Cert using the Private Key
```
openssl req -new -x509 -sha256 -days 365 -key ca-key.pem -out ca.pem
```

## Optional Stage: View Certificate's Content
```
openssl x509 -in ca.pem -text
openssl x509 -in ca.pem -purpose -noout -text
```

# For specific Domain or Subdomain key and cert creation.
## Generate Private Key using RSA encryption w/o a passphrase.
```
openssl genrsa -out cert-key.pem 4096
```
## Generate Certificate Signed Request (CSR).
```
openssl req -new -sha256 -subj "/CN=thukee" -key cert-key.pem -out cert.csr
```
# In this next step we will assume as CA Root that will sign the previously generated CSR. In our case as a self-signed certificate.

## Create a extfile with all the alternative names
```
echo "subjectAltName=DNS:*.thukee.com,IP:192.168.100.181" >> extfile.cnf
```
## Optional
```
echo extendedKeyUsage = serverAuth >> extfile.cnf
```
## Create the self-signed certificate combining all the Public keys,  public keys and certificates generated previusly.
Step 1: (general key)
1. ca-key.pem (private key)
1. ca.pem (private key to certificate conversion)
Setp 2: (specific domain)
1. cert-key.pem (private key)
2. cert.csr (private key to csr conversion)

```
openssl x509 -req -sha256 -days 3650 -in cert.csr -CA ca.pem -CAkey ca-key.pem -out cert.pem -extfile extfile.cnf -CAcreateserial
```
This command will generate the a file **`cert.pem`**

## Now lets create a full-chain trust certificate by combining our private certificate **`ca.pem`** and **`cert.pem`**
```
cat cert.pem > fullchain.pem

cat ca.pem >> fullchain.pem
```
Now the procedure is complete, having the chain of trust **`fullchain.pem`**

# On your server: 

Copy the **`cert-key.pem`** and the **`fullchain.pem`**. This should be the full text inside the file.

# Install the CA Cert as a trusted root CA:
Be sure to copy the ROOT CA on your browser to trust the certificates on your server.

[Fedora](https://docs.fedoraproject.org/en-US/quick-docs/using-shared-system-certificates/)

[Debian/Ubuntu](https://wiki.debian.org/Self-Signed_Certificate)
[Debian/Ubuntu](https://manpages.debian.org/buster/ca-certificates/update-ca-certificates.8.en.html)