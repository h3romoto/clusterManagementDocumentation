# Overview

A SSL certificate is a way to encrypt a site's information and create a more secure connection. Additionally, the certificate can show the virtual private server's identification information to site visitors. Certificate Authorities can issue SSL certificates that verify the server's details while a self-signed certificate has no 3rd party corroboration.

Self-signed cert vs CA:  
> https://en.wikipedia.org/wiki/Self-signed_certificate  
> https://www.globalsign.com/en/ssl-information-center/dangers-self-signed-certificates/  
> https://searchsecurity.techtarget.com/definition/certificate-authority  

# Procedures
1. Obtain the new certificate (need Rab or Nolan) (use `hipercic3-devel`):  
    `certbot certonly --manual --server https://acme-v02.api.letsencrypt.org/directory -d "*.cs.stolaf.edu"`  
    Need Rab or Nolan to finish the challenge  
    Obtain certificate at `/etc/letsencrypt/live/cs.stolaf.edu/`  
    > /etc/letsencrypt/live/cs.stolaf.edu/fullchain.pem	=> certs/cs.stolaf.edu.crt  
    > /etc/letsencrypt/live/cs.stolaf.edu/privkey.pem	=> certs/cs.stolaf.edu.key  
    > /etc/letsencrypt/live/cs.stolaf.edu/chain.pem 	=> certs/cs.stolaf.edu.pem  
    > These can all be done by calling move_cert.sh
    
    Verify certificate: `openssl x509 -in <file .crt or .pem> -text -noout`  
2. Copy the certificate to other servers (`.crt`, `.key`):
    > They’re all mounted from atlas, so no need to actually copy the files
    
    For hipercic.cs.stolaf.edu (hipercic-proxy) (Nginx-based server): (renew_cert_hipercic.sh)  
    > Copy to `/etc/nginx/ssl/private`  
    > Restart the server: `sudo systemctl reload nginx`  
    > Consult here  https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-16-04
    
    For hipercic3-devel.cs.stolaf.edu and hipercic3.cs.stolaf.edu: (renew_cert_nginx.sh)  
    > Copy to `/home/dokku/hipercic3portal/tls`  
    > Restart the server: `sudo systemctl reload nginx`  
    > (nginx hasn’t been up since like early March because 0.0.0.0:80 is already in use by apache2, which seems to just be running the default “it works!” page)
    
    For stogit.cs.stolaf.edu: (renew_cert_stogit.sh)  
    > Copy to `/opt/stogit/config/ssl`  
    > Restart the server: `sudo docker restart stogit`
    
    For hipercic-media.cs.stolaf.edu: (renew_cert_media.sh)  
    > Copy to `/etc/.minio/certs/`     
    > Restart: `docker restart <minio_container>`
    
    For r.cs.stolaf.edu:  
    > Copy to `/etc/letsencrypt/live/r.cs.stolaf.edu/`    
    > Keep file extension as `.pem` (don't change to `.key` and `.cert`)    
    > Run `sudo certbot renew --dry-run`    
    > Restart: `sudo systemctl restart rstudio-server`


