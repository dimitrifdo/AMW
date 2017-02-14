#File Download Protection

File download protection support was added in version 1.1.5, in order to get it working you will need to manually edit your .htaccess file.

## How it works

Every time a user requests a link with .pdf at the end the request is passed through wordpress.

Wordpress will inspect the request, and determine the filename being requested

If that file is stored as an attachment of an existing post, the plugin will determine if that post has any authentication codes assigned to it.

Then the plugin will determine if the user should be allowed to access any authentication codes it found.

If no auth codes were found, the file is delivered.

If the file is not associated with a specific post, the file is delivered since it's impossible to determine if any authentication has been applied.


## .htaccess (for Apache servers)

The feature will not work if you don't add the following lines to your .htaccess

```
RewriteRule ^(.*/)?files/(.*).pdf index.php?file_path=%{REQUEST_URI} [L]
RewriteRule ^(.*/)?uploads/(.*).pdf index.php?file_path=%{REQUEST_URI} [L]
```

Exactly where you put those lines can vary depending on your configuration. For example, the complete .htaccess file might look like this:

```
<IfModule mod_rewrite.c>
    RewriteEngine On
    RewriteRule ^(.*/)?files/(.*).pdf index.php?file_path=%{REQUEST_URI} [L]
    RewriteRule ^(.*/)?uploads/(.*).pdf index.php?file_path=%{REQUEST_URI} [L]
</IfModule>

# BEGIN WordPress
<IfModule mod_rewrite.c>
    RewriteEngine On
    RewriteBase /
    RewriteRule ^index\.php$ - [L]
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteRule . /index.php [L]
</IfModule>
# END WordPress

```
Note: It's important to keep the PDF rewrite rules outside of the Wordpress block, otherwise Wordpress will overwrite your changes every time someone updates the permalinks settings.

## Adding support for other file types (for Apache Servers)

By default, the plugin will only inspect **.pdf** files. Since there is a considerable overhead involved in passing each file request through Wordpress it is not advisable to authenticate for image files like .png, .gif, .jpg etc.

If you have other files that you would like to authenticate, for example **.mp4** you can add the following to your .htaccess

```
RewriteRule ^(.*/)?files/(.*).mp4 index.php?file_path=%{REQUEST_URI} [L]
RewriteRule ^(.*/)?uploads/(.*).mp4 index.php?file_path=%{REQUEST_URI} [L]
```

## Nginx Rules

Add the following rules to your Nginx configuration file, normally in /etc/nginx/sites-enabled

```
   rewrite ^/(.*/)?files/(.*).pdf /index.php?file_path=$uri last;
   rewrite ^/(.*/)?uploads/(.*).pdf /index.php?file_path=$uri last;
```

Save and restart your Nginx server for your changes take effect

## Adding support for other file types (for Nginx Servers)

By default, the plugin will only inspect **.pdf** files. Since there is a considerable overhead involved in passing each file request through Wordpress it is not advisable to authenticate for image files like .png, .gif, .jpg etc.

If you have other files that you would like to authenticate, for example **.mp4** you can add the following to your .htaccess

```
   rewrite ^/(.*/)?files/(.*).mp4 /index.php?file_path=$uri last;
   rewrite ^/(.*/)?uploads/(.*).mp4 /index.php?file_path=$uri last;
```