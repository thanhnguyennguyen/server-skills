### Nginx notes
- Domain configuration
  - Create a domain configuration file at /etc/nginx/sites-available
    ```bash
    server {
      listen       80;
      server_name  YourDomainName.com;
  
      location / {
      proxy_pass          http://localhost:3000; # Proxy : routing all default requests to port 3000
      proxy_set_header    Host              $host;
      proxy_set_header    X-Real-IP         $remote_addr;
      proxy_set_header    X-Forwarded-For   $proxy_add_x_forwarded_for;
      proxy_set_header    X-Client-Verify   SUCCESS;
      proxy_set_header    X-Client-DN       $ssl_client_s_dn;
      proxy_set_header    X-SSL-Subject     $ssl_client_s_dn;
      proxy_set_header    X-SSL-Issuer      $ssl_client_i_dn;
      proxy_set_header    X-Forwarded-Proto http;
      proxy_read_timeout 1800;
      proxy_connect_timeout 1800;
      }
    
      location /api {
      proxy_pass          http://localhost:8080; # Proxy : routing all requests which has path /api to port 8080
      proxy_set_header    Host              $host;
      proxy_set_header    X-Real-IP         $remote_addr;
      proxy_set_header    X-Forwarded-For   $proxy_add_x_forwarded_for;
      proxy_set_header    X-Client-Verify   SUCCESS;
      proxy_set_header    X-Client-DN       $ssl_client_s_dn;
      proxy_set_header    X-SSL-Subject     $ssl_client_s_dn;
      proxy_set_header    X-SSL-Issuer      $ssl_client_i_dn;
      proxy_set_header    X-Forwarded-Proto http;
      proxy_read_timeout 1800;
      proxy_connect_timeout 1800;
      }
    }
    ```
  
    
  - sudo ln -s /etc/nginx/sites-available /etc/nginx/sites-enabled
  - sudo systemctl restart nginx
- Multiple domains
  - Create multiple configuration files in step 1, each file for each domain
- [Setup http password](https://docs.nginx.com/nginx/admin-guide/security-controls/configuring-http-basic-authentication/)

