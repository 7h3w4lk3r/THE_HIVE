# Nginx

## <mark style="color:red;">Disable Unwanted Modules</mark>

When you install nginx, it automatically includes many modules. Currently, you cannot choose modules at runtime. To disable certain modules, you need to recompile nginx. We recommend that you disable any modules that are not required as this will minimize the risk of potential attacks by limiting allowed operations.&#x20;

To do this, use the _configure_ option during installation. In the example below, we disable the _autoindex_ module, which generates automatic directory listings, and then recompile nginx.

```
# ./configure --without-http_autoindex_module
# make
# make install
```

## <mark style="color:red;">Disable nginx server\_tokens</mark>

By default, the _server\_tokens_ directive in nginx displays the nginx version number. It is directly visible in all automatically generated error pages but also present in all HTTP responses in the _Server_ header.

This could lead to information disclosure – an unauthorized user could gain knowledge about the version of nginx that you use. You should disable the _server\_tokens_ directive in the nginx configuration file by setting `server_tokens off`.

## <mark style="color:red;">Exclude Server Headers</mark> <a href="#exclude-server-headers" id="exclude-server-headers"></a>

The default state of a Nginx server returns something like Server: nginx/1.12.1 in the server response or error pages. The best method is to remove the header completely. For that, we require the installation of _**nginx-extras**_.

#### For Debian/Ubuntu

```shell
sudo apt-get install nginx-extras

   
```

#### For RHEL

```shell
 
    yum install nginx-plus-module-headers-more

   
```

After installing and restarting the Nginx service, add the following lines to your _**nginx.conf**_ file.

```shell
 
        # /etc/nginx/nginx.conf
        http {
            # Basic Settings
            more_set_headers 'Server: ';

   
```

If no value is specified, the header will be displayed empty. We can also create custom headers as given below.

```shell
 
        # /etc/nginx/nginx.conf
        http {
            # Basic Settings
            more_set_headers 'Server: Custom Header';

   
```

To completely disable the server header, find server\_token in the Nginx configuration file and set it to server\_tokens off (by removing # in the _**nginx.conf**_ file).

![](<../../../.gitbook/assets/image (221).png>)

## <mark style="color:red;">Control Resources and Limits</mark>&#x20;

To prevent potential DoS attacks on nginx, you can set buffer size limitations for all clients. You can do this in the nginx configuration file using the following directives:

* _**client\_body\_buffer\_size**_ – use this directive to specify the client request body buffer size. The default value is 8k or 16k but it is recommended to set this as low as 1k: `client_body_buffer_size 1k`.
* _**client\_header\_buffer\_size**_** ** – use this directive to specify the header buffer size for the client request header. A buffer size of 1k is adequate for most requests.
* _**client\_max\_body\_size**_ – use this directive to specify the maximum accepted body size for a client request. A 1k directive should be sufficient but you need to increase it if you are receiving file uploads via the POST method.
* _**large\_client\_header\_buffers**_ – use this directive to specify the maximum number and size of buffers to be used to read large client request headers. A `large_client_header_buffers 2 1k` directive sets the maximum number of buffers to 2, each with a maximum size of 1k. This directive will accept 2 kB data URI.

{% hint style="info" %}
Some sources suggest that setting such limits may prevent potential buffer overflow if such vulnerabilities are found in nginx.
{% endhint %}

Disable Any Unwanted HTTP methods We suggest that you disable any HTTP methods, which are not going to be utilized and which are not required to be implemented on the web server. If you add the following condition in the location block of the nginx virtual host configuration file, the server will only allow GET, HEAD, and POST methods and will filter out methods such as DELETE and TRACE.

```
location / {
limit_except GET HEAD POST { deny all; }
}
```

&#x20;Another approach is to add the following condition to the server section (or server block). It can be regarded as more universal but you should be careful with if statements in the location context.

```
if ($request_method !~ ^(GET|HEAD|POST)$ ) {
    return 444; }
```



Note that if ModSecurity does not meet your needs, you can also use other free WAF solutions.

## <mark style="color:red;">Set Up Access and Error Logs</mark>

The nginx access and error logs are enabled by default and are located in _logs/error.log_ and _logs/access.log_ respectively. If you want to change the location, you can use the _error\_log_ directive in the nginx configuration file. You can also use this directive to specify the logs that will be recorded according to their severity level. For example, a _crit_ severity level will cause nginx to log critical issues and all issues that have a higher severity level than _crit_. To set the severity level to _crit_, set the _error\_log_ directive as follows:

```
error_log logs/error.log crit;
```

You can find a complete list of _error\_log_ severity levels in [official nginx documentation](http://nginx.org/en/docs/ngx\_core\_module.html#error\_log).

You can also modify the _access\_log_ directive in the nginx configuration file to specify a non-default location for access logs. Finally, you can use the _log\_format_ directive to configure the format of the logged messages as explained [in nginx documentation](http://nginx.org/en/docs/http/ngx\_http\_log\_module.html#log\_format).

## <mark style="color:red;">Monitor Access and Error Logs</mark>

If you continuously monitor and manage nginx log files you can better understand requests made to your web server and also notice any encountered errors. This will help you discover any attack attempts as well as identify what can you do to optimize the server performance.

You can use log management tools, such as logrotate, to rotate and compress old logs and free up disk space. Also, the _ngx\_http\_stub\_status\_module_ module provides access to basic status information. You can also invest in [nginx Plus](http://nginx.com/products/), the commercial version of nginx, which provides real-time activity monitoring of traffic, load, and other performance metrics.

## <mark style="color:red;">Include Security Headers</mark>

To additionally harden your nginx web server, you can add several different HTTP headers. Here are some of the options that we recommend.

### <mark style="color:orange;">X-Frame-Options</mark>

You use the X-Frame-Options HTTP response header to indicate if a browser should be allowed to render a page in a or an . This could prevent clickjacking attacks. Therefore, we recommend that you enable this option for your nginx server.

To do this, add the following parameter to the nginx configuration file in the server section:

```
add_header X-Frame-Options "SAMEORIGIN";
```

### <mark style="color:orange;">Strict-Transport-Security</mark>

HTTP Strict Transport Security (HSTS) is a method used by websites to declare that they should only be accessed using a secure connection (HTTPS). If a website declares an HSTS policy, the browser must refuse all HTTP connections and prevent users from accepting insecure SSL certificates. To add an HSTS header to your nginx server, you can add the following directive to your server section:

```
add_header Strict-Transport-Security "max-age=31536000; includeSubdomains; preload";
```

### <mark style="color:orange;">CSP and X-XSS-Protection</mark>

\
Content Security Policy (CSP) protects your web server against certain types of attacks, including Cross-site Scripting attacks (XSS) and data injection attacks. You can implement CSP by adding the following example Content-Security-Policy header (note that the actual header should be configured to match your unique requirements):

```
add_header Content-Security-Policy "default-src 'self' http: https: data: blob: 'unsafe-inline'" always;
```

The HTTP _X-XSS-Protection_ header is supported by IE and Safari and is not necessary for modern browsers if you have a strong Content Security Policy. However, to help prevent XSS in the case of older browsers (that don’t support CSP yet), you can add the X-XSS Protection header to your _server_ section:

```
add_header X-XSS-Protection "1; mode=block";
```

## <mark style="color:red;">Configure SSL and Cipher Suites</mark>

The default configuration of nginx allows you to use insecure old versions of the TLS protocol (according to the official documentation: ssl\_protocols TLSv1 TLSv1.1 TLSv1.2). This may lead to attacks such as the BEAST attack. Therefore, we recommend that you do not use old TLS protocols and change your configuration to support only newer, secure TLS versions.

To do this, add the following directive in the server section of the nginx configuration file:

```
ssl_protocols TLSv1.2 TLSv1.3;
```

Additionally, you should specify cipher suites to make sure that no vulnerable suites are supported. To select the best cipher suites, read our article on TLS cipher hardening and add a ssl\_ciphers directive to the server section to select the ciphers (as suggested in the article on cipher hardening). We also recommend that you add the following directive to the server section:

```
ssl_prefer_server_ciphers on;
```

This directive will let the decision on which ciphers to use be made server-side not client-side.

## <mark style="color:red;">Check Configurations with Gixy</mark>

Gixy is an open-source tool that lets you check your nginx web server for typical misconfigurations. After you prepare your nginx configuration, it is always a good idea to check it with Gixy.

[You can find Gixy here](https://github.com/yandex/gixy).
