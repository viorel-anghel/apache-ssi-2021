# Apache SSI - 20 years after

Apache Server Side Includes (SSI) is a technique to add dynamic content in HTML documents. I was using this more than 20 years ago and that makes a nice Alexandre Dumas style title. 

I was surprised to find out that it is very hard to setup this as most of the documentation you can find is either unclear or incomplete or both.

So, I present you: 

## How to setup and use Apache Server Side Includes in Ubuntu 20.04

### Server setup

We'll start with the official tutorial from https://httpd.apache.org/docs/current/howto/ssi.html . 

SSI can be configured either in the main configuration files or in some files called `.htaccess` in any directory. We'll use the first variant, main configuration files.

I'm testing this on a new VM with Ubuntu 20.04. Upgrade all packages and install the `apache2` package:

```
apt-get update -y
apt-get upgrade -y
apt-get install -y apache2
``` 

The documentation says now to add this line `Options +Includes` but it is not very clear where to do that. The maintainers from Debian/Ubuntu decided to split the apache configuration into multiple files and directories so this is going to be distribution specific.

Edit the file `/etc/apache2/sites-enabled/000-default.conf` (yes, I know it's a symbolic link) and add those lined after the line with `DocumentRoot /var/www/html`:

```
<Directory /var/www/html>
  Options +Includes
  AddType text/html .shtml
  AddOutputFilter INCLUDES .shtml
</Directory>
```

The file is also added in this git reposiroty and you may check its history.

Restart the apache server with `systemctl restart apache2`.

Now, create a file `/var/www/html/test-ssi.shtml` with this content:

```
<html>
<head>
  <title>ssi test</title>
</head>
<body>
  <h1>ssi test</h1>

  <p>Server date is: <!--#echo var="DATE_LOCAL" --> </p>

</body>
</html>
```



