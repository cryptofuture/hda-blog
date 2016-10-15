---
title: Encrypted connection between Back-End and Front-End with nginx
tags:
  - nginx
  - certificates
  - ssl
  - tls
date: 2016-08-27
---
Not very rare topic for post, but I didn't found clear instructions anywhere, while I'll try to make things clear as possible. I think main reason for this is because in most cases, you don't need encryption or don't want to bother with encryption since front-end and back-end located in same local network, or even on same computer.

First we need own Certification Center, valid and trusted certificate placed on Front-End, but for front-end -> back-end communication, self-made certificates will be enough. 

```bash
openssl genrsa -aes256 -out rootCA.key 4096
openssl req -x509 -new -nodes -key rootCA.key -out rootCA.pem -days 3650
``` 
<!--more-->
> Common Name should be something like "MyCoolName Certification Center" for Certification Authority, and not your domain.tld. With domain.tld common name in CA you can't sign domain.tld, but only your subdomains, it will be like self-signed certificate that way.

Check `-days 3650` here, that means CA certificate will be valid for 10 years, and this is quite important, since after your CA certificate will become outdated, all signed certificates will be outdated too, regardless of issued period. You can check certificate expiration with `openssl x509 -enddate -noout -in certificate.crt` command. Also, you may be interested in [this thread](https://serverfault.com/questions/306345/certification-authority-root-certificate-expiry-and-renewal). 

Let me make, another interruption here, but when you need to generate hell a lot of certificates, you better make configuration file first.

```
[req_distinguished_name]
countryName = 
stateOrProvinceName = 
localityName = 
organizationName = 
organizationalUnitName = 
commonName = domain.tld
commonName_max	= 64
emailAddress = 
emailAddress_max = 40

[req]
distinguished_name = req_distinguished_name
req_extensions = v3_req

[v3_req] 
keyUsage = keyEncipherment, dataEncipherment
extendedKeyUsage = serverAuth
```

After that you can just add `-config configuration.cnf` to provide configuration for every CSR, with only Common Name change. Another thing you should note here, your need to retype common name even if your planned common name is the same as configuration common name, to avoid this error: `no objects specified in config file problems making Certificate Request`

With own CA, is a time to make certificates for back-end and front-end

```bash
# Back-End
openssl genrsa -aes256 -out server.key 4096
# Common Name is the same as nginx `server_name` for certificates
openssl req -new -key server.key -out server.csr
# Or with configuration file:
openssl req -new -key server.key -out server.csr -config configuration.cnf
# plain private key for installation
cp server.key server.key.org; openssl rsa -in server.key.org -out server.key
# Sign for one year with our CA
openssl x509 -req -days 365 -in server.csr -CA rootCA.pem -CAkey rootCA.key -set_serial 01 -out server.crt
```
```bash
# Front-End
openssl genrsa -aes256 -out client.key 4096
openssl req -new -key client.key -out client.csr -config configuration.cnf
cp client.key client.key.org; openssl rsa -in client.key.org -out client.key
openssl x509 -req -days 365 -in client.csr -CA rootCA.pem -CAkey rootCA.key -set_serial 02 -out client.crt
```
> I prefer odd numbers for back-ends serials, and even for front-ends. But actually there no difference between certificate generation for front-end or back-end.

Now is a time to install our certificates.

```bash
# Back-End example:
server {
    # We don't need to run extra http support on back-end
	listen  443 ssl http2 reuseport;

	server_name domain.tld;
	ssl_certificate /path/server.crt;
	ssl_certificate_key /path/server.key;
	ssl_client_certificate /path/ca.crt; #rootCA.pem
	ssl_verify_client on;
	ssl_verify_depth 2;

	root /path/public_html;
	error_log /var/log/nginx/domain.log;
	access_log /var/log/nginx/domain.log;
	index index.html;
}
```

```bash
# Front-End example:
upstream domain.tld  {
	server ip:port;
}

server {
    # http to https redirect, 
    # You can use HSTS header on backend also
	server_name domain.tld;
	return 301 https://domain.tld$request_uri;
}

server {
    # www to non www redirect
	server_name www.domain.tld;
	return 301 https://domain.tld$request_uri;
	listen 443 ssl http2;
    # Valid and trusted certificate
	ssl_certificate /path/domain.crt;
	ssl_certificate_key /path/domain.key;

    # OCSP Stapling (not in range of current post, but could be useful for you)
	#ssl_stapling on;
	#ssl_stapling_verify on;
	#ssl_trusted_certificate /path/valid-and-trusted-ca-certificate.crt;
    #resolver 8.8.4.4 8.8.8.8;
    
    # Cloudflare Authenticated Origin Pulls (not in range of current post, but could be useful for you)
	#ssl_client_certificate /path/cloudflare-origin-pulls.crt;
	#ssl_verify_client on;
}

server {
	listen 443 ssl http2;
	server_name domain.tld;
    # Valid and trusted certificate
	ssl_certificate /path/domain.crt;
	ssl_certificate_key /path/domain.key;

    # OCSP Stapling (not in range of current post, but could be useful for you)
	#ssl_stapling on;
	#ssl_stapling_verify on;
	#ssl_trusted_certificate /path/valid-and-trusted-ca-certificate.crt;
    #resolver 8.8.4.4 8.8.8.8;
    
    # Cloudflare Authenticated Origin Pulls (not in range of current post, but could be useful for you)
	#ssl_client_certificate /path/cloudflare-origin-pulls.crt;
	#ssl_verify_client on;

	error_log /var/log/nginx/domain.log;
	access_log /var/log/nginx/domain.log;

	location / {
	proxy_pass https://domain.tld/;
	proxy_set_header Host $host;
	proxy_set_header X-Real-IP $remote_addr;
	proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
	proxy_ssl_certificate /path/client.crt;
	proxy_ssl_certificate_key /path/client.key;
	proxy_ssl_trusted_certificate /path/ca.crt; #rootCA.pem
	proxy_ssl_verify on;
	proxy_ssl_verify_depth 2;
	proxy_ssl_session_reuse on;
	}
}
```

```bash
# Front-End shorter example
upstream domain.tld  {
	server ip:port;
}
server {
...
    location / {
	proxy_pass https://domain.tld/;
	proxy_set_header Host $host;
	proxy_set_header X-Real-IP $remote_addr;
	proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
	proxy_ssl_certificate /path/client.crt;
	proxy_ssl_certificate_key /path/client.key;
	proxy_ssl_trusted_certificate /path/ca.crt; #rootCA.pem
	proxy_ssl_verify on;
	proxy_ssl_verify_depth 2;
	proxy_ssl_session_reuse on;
	}
}    
```

#### More
> If you run back-end in container (for example lxc/lxd) this iptables rule could be useful for you:
`iptables -t nat -A PREROUTING -s front-end-ip/32 -d your_public_ip -p tcp --dport port_for_nginx_upstream_configuration -j DNAT --to-destination 10.x.x.x:443`

You could block access to back-end from any location, beside front-end.

```bash
# Example
iptables -A INPUT -s front-end-ip/32 -p tcp -m tcp --dport port_you_bind_nginx -j ACCEPT
iptables -A INPUT -i lo -j ACCEPT
iptables -A INPUT -p icmp -j ACCEPT
iptables -A INPUT -p icmp -m icmp --icmp-type 5 -j DROP
iptables -A INPUT -p tcp -m tcp --dport ssh_bind_port -j ACCEPT
iptables -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
iptables --policy INPUT DROP
```
More above you can block access from any location beside from your CDN on front-end.  
For cloudflare you could check ip ranges [here](https://www.cloudflare.com/ips/), and setup [tls authenticated origin pulls](https://blog.cloudflare.com/protecting-the-origin-with-tls-authenticated-origin-pulls/).






