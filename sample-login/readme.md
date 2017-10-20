
# Sample Spring Boot application with a login form and Let's Encrypt for SSL certificates. 

See https://blog.microideation.com/2016/09/23/zero-cost-verified-https-using-letsencrypt-and-nginx-for-tomcat-8/

**Build docker image:**   
  docker build -t chregli/sample-login:latest .

**Create volumes:**   
  docker volume create pv1
  sudo ls -l /var/lib/docker/volumes/pv1/_data/live/test.snoopfish.ch
  docker volume create pv2
  sudo ls -al /var/lib/docker/volumes/pv2/_data/test.snoopfish.ch

**Start container:**   
  docker run --name sample-login -p 80:80 -p 443:443 -v pv1:/etc/letsencrypt -v pv2:/var/www -d chregli/sample-login:latest
  
===

**Log into AWS instance:**   
ssh -i ${KEYPAIR.pem} ec2-user@ec2-35-156-253-107.eu-central-1.compute.amazonaws.com

**Create certificate:**   
certbot certonly --webroot -w /var/www/test.snoopfish.ch -d test.snoopfish.ch  
   => /etc/letsencrypt/live/test.snoopfish.ch
   
**Renew certificate:**      
certbot renew --pre-hook "service nginx stop" --post-hook "service nginx start" --quiet

