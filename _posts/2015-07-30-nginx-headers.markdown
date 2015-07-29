---
layout: post
title:  "Nginx CORS headers, proxy_pass and if module"
date:   2015-07-30 01:11:54
categories: 
---

1. Setting up reverse proxy in nginx is straight forward using the proxy_pass directive.

       location /reversed-proxy {
         proxy_pass http://example.com;
       }

2. Adding CORS headers on proxy_pass. More information about HTTP access control here: [https://developer.mozilla.org/en-US/docs/Web/HTTP/Access_control_CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/Access_control_CORS)

        location /reversed-proxy {
          proxy_pass http://example.com;
          add_header 'Access-Control-Allow-Origin' '*';
          add_header 'Access-Control-Allow-Methods' 'POST,GET,OPTIONS';
        }

3. The above example allows cross-domain requests from all websites. The right way to restrict them is to use regex match on the origin:

        location /reversed-proxy {
          proxy_pass http://example.com;
          if ($http_origin ~* 'https?://(www\.foobar\.com)') {
            add_header 'Access-Control-Allow-Origin' "$http_origin";
          }
          add_header 'Access-Control-Allow-Methods' 'POST,GET,OPTIONS';
        }

4. You may also want to ensure that preflight requests from browsers are handled properly. Please read the mozilla link above to find out what is a preflight request and in what condition is it sent to the server.


        location /reversed-proxy {
          proxy_pass http://example.com;
          if ($http_origin ~* 'https?://(www\.foobar\.com)') {
            add_header 'Access-Control-Allow-Origin' "$http_origin";
          }
          
          #preflight request 
          if ($request_method = 'OPTIONS') {
            add_header 'Access-Control-Max-Age' '1728000';
            add_header 'Content-Type' 'text/plain charset=UTF-8';
            add_header 'Content-Length' '0';
            return 204;
          }
          add_header 'Access-Control-Allow-Methods' 'POST,GET,OPTIONS';
        }

    The snippet 3) and 4) may look harmless to a person with imperative langauges background such as java, javascript, but it is completely broken!
    
    For e.g. In case of preflight request, the response headers will not have the `'Access-control-allow-origin'` header. Because,
    Nginx declaratively finds the right `if` block to execute, in preflight request's case it will be the second `if` block and it executes only its contents imperatively.

    There is also another quirk, if its a GET request originating from foobar.com, nginx will match it with the first if block - declaratively and request to proxy_pass will be sent as `example.com/reversed-proxy/$1` instead of `example.com/$1`, because the if block acts as a nested location block and `/reversed` is treated as url path. 

5. Such errors have been documented extensively - [http://wiki.nginx.org/IfIsEvil](http://wiki.nginx.org/IfIsEvil) Hence, examples such as these [https://gist.github.com/alexjs/4165271](https://gist.github.com/alexjs/4165271) and [https://gist.github.com/michiel/1064640](https://gist.github.com/michiel/1064640) don't seem to work in the context of proxy_pass because of the issue with if block.

6. If we want restrictive CORS accesss + reverse proxy + nginx then we cannot use `if` block at all. The best alternative is to use `map` module. More information here: [http://nginx.org/en/docs/http/ngx_http_map_module.html](http://nginx.org/en/docs/http/ngx_http_map_module.html). It works like a pattern matching block and fits perfectly well in CORS scenario.

    The solution is given in stackoverflow: [https://serverfault.com/questions/674900/nginx-if-statement-inside-location-returns-404/674901#674901](https://serverfault.com/questions/674900/nginx-if-statement-inside-location-returns-404/674901#674901), documenting in the blog for completeness. To solve the broken snippet above, we should do this:

        map $http_host $cors_header {
          hostnames;
          default "";
          .foobar.com "$http_origin";
        }

        server {
          location /reversed-proxy {
            proxy_pass http://example.com;
            add_header 'Access-Control-Allow-Origin' $cors_header;
            add_header 'Access-Control-Allow-Methods' 'POST,GET,OPTIONS';

            #preflight request 
            if ($request_method = 'OPTIONS') {
              add_header 'Access-Control-Max-Age' '1728000';
              add_header 'Content-Type' 'text/plain charset=UTF-8';
              add_header 'Content-Length' '0';

              add_header 'Access-Control-Allow-Origin' $cors_header;
              add_header 'Access-Control-Allow-Methods' 'POST,GET,OPTIONS';
              return 204;
            }
          }
        }

    notice the two access control headers duplicated inside and outside the `if` block, its not a mistake. Its because nginx finds the `if` block to execute declaratively and skips the code outside the block.

   Thats all.
