# Setting up a SSL certificate with an apache proxy server

You can add the following anywhere that can be read by apache.conf

```ApacheConf
<VirtualHost *:443>
    ServerName subsite.sudh33ra.com
    SSLEngine on
        SSLCertificateFile /home/sudh33rauser/starsudh33ra.crt
        SSLCertificateKeyFile /home/sudh33rauser/starsudh33ra.key
        SSLCertificateChainFile /home/sudh33rauser/starsudh33ra_ca.crt
    <IfModule mod_proxy.c>
        ProxyRequests Off
        ProxyPass         /   http://localhost:9091/
        ProxyPassReverse  /   http://localhost:9091/
    </IfModule>
</VirtualHost>
```

If you want to force https

```ApacheConf
<VirtualHost *:80>
   ServerName subsite.sudh33ra.com
   Redirect permanent / https://subsite.sudh33ra.com/
</VirtualHost>
```
