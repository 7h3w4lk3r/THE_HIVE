# Apache mod\_rootme

On a web server, `mod_rootme` Apache module ([https://github.com/sajith/mod-rootme](https://github.com/sajith/mod-rootme)) can be installed for a privileged backdoor. Although Apache is usually not running as root, `mod_rootme` enables root access by using a pre-fork hook to take advantage of the master Apache process when it still has root permissions.

Once compiled, copy `mod_rootme.so` to the Apache modules directory (often `/usr/lib/apache2/modules`) and add the following string to the Apache config file (usually `/etc/apache2/apache2.conf` or `/etc/httpd/conf/httpd.conf`):

`LoadModule rootme_module /usr/lib/apache2/modules/mod_rootme.so`

To use the backdoor, connect to Apache using NetCat and type `get root`.
