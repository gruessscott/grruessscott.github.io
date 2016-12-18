---
layout: post
title: Viewing Certificates with OpenSSL
category: tech
tags: openssl certificates pki
---

The openssl cli has lots of options and I can never seem to remember them.
So this is just a (rather disorganized) collection of tips and tricks...

# Certificate Signing Request (CSR)

	openssl req -text -noout -in hostcsr.pem

View the contents of a certificate:

    openssl x509 -text -noout -in hostcert.pem

View the Signer of a Certificate

    openssl x509 -in cert.pem -noout -issuer -issuer_hash

Verify a Certificate Matches a Private Key:

    openssl x509 -in cert.pem -noout -modulus
    openssl rsa -in key.pem -noout -modulus

Find the Hash Value of a Certificate

    openssl x509 -nout -hash -in cert.pem

    #creating a link
    ln -s cert.pem `openssl x509 -noout -hash -in cert.pem`.0

Converting from one form to another:

    openssl x509 -inform der -outform pem -in dev-root-ca-8.der -out dev-root-ca-8.pem

Remove a passphrase from a key:

	# removing passphrase from private key file
	openssl rsa -in key.pem -out newkey.pem

	# remove passphrase from pem containing private key and public certificate:
	openssl rsa -in mycert.pem -out newcert.pem
	openssl x509 -in mycert.pem >> newcert.pem

# Checking the Modulus of the Private Key

These commands allow you to compare a private key with its corresponding
CSR and certificate.

    openssl x509 -noout -modulus -in certificate.crt | openssl md5
    openssl rsa -noout -modulus -in privateKey.key | openssl md5
    openssl req -noout -modulus -in CSR.csr | openssl md5

# Mac OSX

## Fetching certificates from login keychain

    security find-certificate -a -p ~/Library/Keychains/login.keychain > allcerts.pem

# Converting Certificates with OpenSSL

## Certificates 

Converting PEM encoded certificate to DER

    openssl x509 -outform der -in certificate.pem -out certificate.der
 
Converting DER encoded certificate to PEM

    openssl x509 -inform der -in certificate.cer -out certificate.pem
 
Converting PEM encoded certificates to PKCS7 (P7B)

    openssl crl2pkcs7 -nocrl -certfile certificate.cer -out certificate.p7b -certfile CACert.cer
 
Converting PKCS #7 (P7B) to PEM encoded certificates

    openssl pkcs7 -print_certs -in certificate.p7b -out certificate.cer
 
## Certificates and Keys

Converting PEM encoded Certificate and private key to PKCS #12 / PFX

    openssl pkcs12 -export -out certificate.pfx -inkey privateKey.key \
        -in certificate.crt -certfile CACert.crt
 
Converting PKCS #7 (P7B) and private key to PKCS #12 / PFX

    openssl pkcs7 -print_certs -in certificate.p7b -out certificate.cer
    openssl pkcs12 -export -in certificate.cer -inkey privateKey.key \
        -out certificate.pfx -certfile CACert.cer
 
Converting PKCS #12 / PFX to PKCS #7 (P7B) and private key

    openssl pkcs12 -in certificate.pfx -out certificate.cer -nodes

# Checking TLS on a website with an alternate trust chain

Note: Tip from [http://www.cyberciti.biz/faq/test-ssl-certificates-diagnosis-ssl-certificate/]

	$ openssl s_client -CApath ~/.cert/mail.nixcraft.net/ -connect mail.nixcraft.net:443

# Credits / Reference

* [http://security.ncsa.illinois.edu/research/grid-howtos/usefulopenssl.html]
* [http://blog.burghardt.pl/2010/03/ncipher-hsm-with-openssl/]
* [http://www.cyberciti.biz/faq/test-ssl-certificates-diagnosis-ssl-certificate/]
