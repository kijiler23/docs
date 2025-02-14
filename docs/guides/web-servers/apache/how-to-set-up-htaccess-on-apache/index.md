---
slug: how-to-set-up-htaccess-on-apache
author:
  name: Linode
  email: docs@linode.com
description: 'Use the Apache .htaccess file to configure a website on a per-directory basis. This guide shows you how to create and enable an .htaccess file and more.'
keywords: ["htaccess", " apache"]
license: '[CC BY-ND 4.0](https://creativecommons.org/licenses/by-nd/4.0)'
published: 2017-09-25
modified: 2022-07-01
modified_by:
  name: Linode
title: 'Enable and Set Up the .htaccess File on Apache'
title_meta: 'How to Set Up the htaccess File on Apache'
contributor:
  name: Christopher Piccini
  link: https://twitter.com/chrispiccini11
external_resources:
- '[HTTP Error and Status Codes](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)'
- '[Apache .htaccess Documentation](https://httpd.apache.org/docs/current/howto/htaccess.html)'
tags: ["web server","apache"]
aliases: ['/web-servers/apache/how-to-set-up-htaccess-on-apache/']
---

![How to Set Up the htaccess File on Apache](how-to-set-up-the-htaccess-file-on-apache-smg.jpg)

## What is the htaccess File?

An .htaccess file makes Apache web server configuration changes on a per-directory basis. This allows you to configure specific areas of a website without having to modify the Apache web server's primary configuration files. This guide shows you how to create and enable an .htaccess file. You also learn how to use the .htaccess file to configure website file structure permissions, redirects, and IP address restrictions.

## Before You Begin

1.  If you have not already done so, create a Linode account and Compute Instance. See our [Getting Started with Linode](/docs/guides/getting-started/) and [Creating a Compute Instance](/docs/guides/creating-a-compute-instance/) guides.

1.  Follow our [Setting Up and Securing a Compute Instance](/docs/guides/set-up-and-secure/) guide to update your system. You may also wish to set the timezone, configure your hostname, create a limited user account, and harden SSH access.

1.  Complete the Apache section in the [Install a Lamp Stack](/docs/guides/how-to-install-a-lamp-stack-on-ubuntu-18-04/) to install Apache on your Linode.

{{< note respectIndent=false >}}
Throughout this guide, replace each instance of `testuser` with your custom user account. Replace each occurrence of `example.com` with the IP address or Fully Qualified Domain Name (FQDN) of your Linode.
{{< /note >}}

## Enable the Apache .htaccess File

To enable the .htaccess file, you must update your website's [Virtual Hosts](/docs/guides/how-to-install-a-lamp-stack-on-ubuntu-20-04/#virtual-hosts) file to add an `AllowOverride All` directive.

1. Use a text editor to open your configuration file:

        sudo nano /etc/apache2/sites-available/example.com.conf

1.  After the VirtualHost block (</VirtualHost>) add the `AllowOverride All` directive as shown below:

    {{< file "/etc/apache2/sites-available/example.com.conf" >}}
....
</VirtualHost>
<Directory /var/www/html/example.com/public_html>
    Options Indexes FollowSymLinks
    AllowOverride All
    Require all granted
</Directory>

{{< /file >}}

1.  Save the file, then restart apache:

        sudo service apache2 restart

## Create the .htaccess File

Once you have enabled support for .htaccess files in your Apache configuration file, you can create the .htaccess file.

Use a text editor to create and open the .htaccess file. You can place the file in your site's document root. If you followed the steps in one of our LAMP stack guides, your root folder is located in the `/var/www/html/example.com/public_html/` directory. Replace `example.com` with the your own site's domain name.

    sudo nano /var/www/html/example.com/public_html/.htaccess

## Disable Directory Listings in Apache

Apache's [mod_autoindex module](https://httpd.apache.org/docs/2.4/mod/mod_autoindex.html) generates a directory listing for any URL that does not contain a directory index file.  Typically, Apache is configured to use a file named `index.html` as the source for a directory listing. If this file is not present, and the mod_autoindex module is installed and enabled, Apache automatically generates a directory listing for the given URL. It's a security best practice to disable the directory listing generated by the mod_autoindex module. This way only site visitors that are familiar with your site's directory and file structure can locate and access site files that you do not directly provide links to.

One way to restrict a directory listing is through Apache's .htaccess file. This section shows you how to update an .htaccess file to disable directory listings.

{{< note respectIndent=false >}}
CMS systems such as [WordPress](/docs/guides/how-to-install-wordpress-ubuntu-2004/) create .htaccess configurations by default. This guide assumes that no .htaccess file exists. If you have not created an .htaccess file, follow the steps in the [Create the .htaccess File](/docs/guides/how-to-set-up-htaccess-on-apache/#create-the-htaccess-file) section of this guide.
{{< /note >}}

1. Using your preferred text editor, open your .htaccess file and add the following configuration:

    {{< file "/var/www/html/example.com/public_html/.htaccess" >}}
Options -Indexes

{{< /file >}}

1.  Now, navigate to your site and view the **Forbidden** message that appears. In order to access the different areas of your site, you are now required to specifically indicate the file or directory path.

## Restrict IPs

This section shows you how to restrict specific IPs from accessing your site. This is useful if you want to block certain visitors from viewing your site. You can also set this up to prevent certain IPs from accessing specific sections of your site.

{{< note respectIndent=false >}}
Subdirectories inherit settings from a parent directory's .htaccess file. A parent directory's .htaccess file can be overridden by a subdirectory if it contains its own, separate .htaccess file. The examples in this section uses an .htaccess file located in a website's document root directory. If you have multiple .htaccess files, you should carefully consider in which .htaccess file you should each Apache directive you wish to use.
{{< /note >}}

### Block IPs with htaccess File

1.  Create or edit the .htaccess file located in your website's document root:

        cd /var/www/html/example.com/public_html/
        sudo nano .htaccess

1.  Delete the `Options -Indexes` line from the previous section (if applicable) and add the following lines to block the target IP addresses:

    {{< file "/var/www/html/example.com/public_html" >}}
order allow,deny

# Denies the IP 192.0.2.9
deny from 192.0.2.9

# Denies all IP's from 192.0.2.0 through 192.0.2.255
deny from 192.0.2

{{< /file >}}

### Allow IPs with htaccess File

1.  Create or edit the .htaccess file located in the web directory where you want this setting to be applied.

1.  Add the following lines to deny all IPs except for the specific IP and pool of IPs mentioned in the command:

    {{< file "/var/www/html/example.com/public_html" >}}
order deny,allow

# Denies all IP's
Deny from all

# Allows the IP 192.0.2.9
allow from 192.0.2.9

# Allows all IP's from 192.0.2.0 through 192.0.2.255
allow from 192.0.2

{{< /file >}}

## Configure Redirects with htaccess

You can redirect traffic using the .htaccess configuration file. In the below example, you update the .htaccess file for the root directory of your website to redirect a visitor to `http://example.com/test1/index.html` if they try to visit `http://example.com/main.html`.

{{< note respectIndent=false >}}
Ensure that your website has a landing page. In the following steps, replace each instance of `main.html` with the landing page of your website.
{{< /note >}}

1.  Create a test html file to redirect a visitor to `http://example.com/test1/index.html`:

        mkdir test1
        sudo touch test1/index.html

1.  Add some basic content to the test html file:

    {{< file "/var/www/html/example.com/public_html/test1/index.html" >}}
<!doctype html>
<html>
  <body>
    This is the html file in test1.
  </body>
</html>

{{< /file >}}

1.  Open the .htaccess file in your project's root directory. Remove all existing configurations in this file and add the following line:

    {{< file "/var/www/html/example.com/public_html/.htaccess" >}}
Redirect 301 /main.html /test1/index.html

{{< /file >}}

The first parameter after the 'Redirect' command is the HTTP status code. Specifying a status code is helpful for letting the browser know that the page has been moved to a new location. If you leave this parameter blank, it defaults to a 302 code indicating that the redirect is temporary. Specifying 301 makes it clear that the page at the requested location has permanently moved to a new location.

The next parameter is the Unix path to the file that is requested in the URL. This parameter requires that it is a Unix path and not a URL. The path should be the location of the .htaccess file where the redirect configuration is set up. The final parameter indicates where you want the visitor to be redirected. In this case, the traffic is being redirected to `/test1/index.html`; for this second parameter a Unix path or HTTP URL is acceptable.

1.  Navigate to `example.com/main.html` in a browser. You should see the url redirect to `example.com/test1/index.html` in the address bar, and your test html file should be displayed.

### Set the Apache 404 Error Page

When a visitor attempts to access a page or resource that doesn't exist (for example by following a broken link or typing an incorrect URL,) the server responds with a 404 error code. It is important that users receive feedback explaining the error. By default, Apache displays an error page in the event of a 404 error. However, most sites provide a customized error page. You can use .htaccess settings to let Apache know what error page you would like displayed whenever a user attempts to access a nonexistent page.

1.  The configuration below redirects all requests for nonexistent documents to a page in the project root directory called `404.html`. Open the `.htaccess` file and add the following line:

    {{< file "/var/www/html/example.com/public_html/.htaccess" >}}
ErrorDocument 404 /404.html

{{< /file >}}

1.  Create the `404.html` file:

    {{< file "/var/www/html/example.com/public_html/404.html" >}}
<!doctype html>
<html>
  <body>
    404 Error: Page not found
  </body>
</html>

{{< /file >}}

1.  In a browser, navigate to a page that does not exist, such as `www.example.com/doesnotexist.html`. The 404 message should be displayed.
