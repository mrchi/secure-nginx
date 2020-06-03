A secure Nginx configuration example for HTTPS website, generated by https://nginxconfig.io/

The HTTPS certificate is provided by [Let's Encrypt](https://letsencrypt.org/) using [certbot](https://certbot.eff.org/).

You may need to use `sudo` to run the following commands.

# How to use

## Update Nginx configurations

First, check your domain has been resolved correctly
```
dig subdomain.example.conf
```

Clone this repo
```
git clone https://github.com/mrchi/secure-nginx.git
```

Assuming you have backed up the files, then copy these configuration to `/etc/nginx`
```
cp -R secure-nginx/nginx/* /etc/nginx
```

Create your own site conf file from `subdomain.example.com.conf` example, you need to change "server_name" and specific proxy rules in HTTPS server part(usually listen port 443).

Comment entire HTTPS server part. Then make a soft link in `sites-enabled` directory and reload Nginx
```
nginx -t && systemctl reload nginx
```

Now you can try to access your site by HTTP. It will redirect to HTTPS but can't open any page, because we commented the HTTPS part.

## Apply for HTTPS certificate

Create directory for certbot webroot validation
```
mkdir -p /var/www/_letsencrypt
chown www-data /var/www/_letsencrypt
```

Follow [certbot instruction](https://certbot.eff.org/instructions) to install certbot on your system.

Now apply for HTTPS certificate
```
certbot certonly --webroot -d YOUR_DOMAIN --email YOUR_EMAIL -w /var/www/_letsencrypt -n --agree-tos --force-renewal
```

Uncomment HTTPS server part in site conf file, and reload Nginx
```
nginx -t && systemctl reload nginx
```

Now you can access your site by HTTPS, enjoy it!

# One more step

Configure certbot to reload Nginx after success renew
```
echo -e '#!/bin/bash\nnginx -t && systemctl reload nginx' | tee /etc/letsencrypt/renewal-hooks/post/nginx-reload.sh
chmod a+x /etc/letsencrypt/renewal-hooks/post/nginx-reload.sh
```
