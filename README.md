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

Enable the `include` module and restart the apache server:
```
a2enmod include
systemctl restart apache2
```

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

Access it in a browser. You may even test locally with `curl localhost/test-ssi.shtml`. It should show a nice timestamp.

### SSI Exec

Let's try something more dynamic, let's try running a shell script from the HTML page. 

Create a file `/info.sh` (at that exact location, /) with this content:
```bash
#!/bin/bash
hostname
uname -a
uptime
hostname -I
```

It's a simple shell script and to run it you need to make it executable `chmod 755 /info.sh` and then you can run it using the full path. `/info.sh` should show some system info.

To run this from a web page and see the result in a browser, create a file `/var/www/html/test-ssi-exec.shtml` like this:

```
<html>
<head>
  <title>ssi test</title>
</head>
<body>
  <h1>ssi test</h1>

  <p>Server date is: <!--#echo var="DATE_LOCAL" --> </p>

<pre>
Server info:
<!--#exec cmd="/info.sh" -->
</pre>

</body>
</html>
```

But when you try to access it, for example `curl localhost/test-ssi-exec.shtml`, the exec part will give an error:
```
[an error occurred while processing this directive]
```

One last step is needed in this battle with SSI, enable the cgi module and this will permit SSI+exec:
```
a2enmod cgi
systemctl restart apache2

curl localhost/test-ssi-exec.shtml
```

### the end
This is it, you are now ready to try other tricks from the official SSI documentation.




