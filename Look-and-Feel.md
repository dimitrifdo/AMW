Customizing the look and feel of the login box and other elements is easy.

In the folder for this plugin you will see a 'theme' directory. Copy all the files from this directory into the directory for the active theme on your site. 

Now you can make changes to the login box within your theme directory and you won't be affected when the authentication plugin is updated or overwritten.

By default, the login box will replace the body content of a page.

# Theme Files

### mw-login-block.php

* The main login box that appears in the body content. Be careful when editing this, the form fields are necessary to make it work.
* You are advised to move the CSS styles from this file into your proper stylesheet for your theme.

```php
    <h2>
        <?php echo (isset($title)) ? $title : "Login" ; ?>
    </h2>
	<div class="<?php echo (isset($message_class)) ? $message_class : ''; ?>">
		<?php if(isset($message)) echo $message; ?>
	</div>
```
* This section looks for error messages coming back from wordpress and displays them to the user. e.g. 'Incorrect Password' errors etc.  See the [Messages and Language Variables Page](https://github.com/Pubsvs/Middleware-Authentication/wiki/Messages-and-Language-Variables) for more information on customising the messages.

```php
	<?php wp_login_form(); ?>
```
* This function is native to wordpress and outputs the form fields. There are classes an IDs that you can use to style this output the way you want.

```php
	<p>
		<a href="<?php site_url() ;?>mw/login/password_reset/">
			<?php _e('Forgot Your Password?'); ?>
		</a>
	</p>
```
* A link to the forgot password page. 

### mw-login-widget.php
This template controls the look and feel of the sidebar widget in your site. 
If your theme is properly configured you shouldn't have to do much with this file.

### mw-password-reset.php

The password reset form prompts the user for their email address. It also displays feedback if the form succeeds, or fails.

### mw-user-logged-in.php
This template is displayed on the login page if the user is already logged in. For most sites, this can be ignored.


# Redirect After login

When a user successfully logs in they are redirected to different pages depending on where they came from

## Redirect after requesting protected content

When a user requests some content and they are not logged in, or they don't have permission to access it, they will see a login box.

On successful login, they will be redirected back to the page they originally requested.

## Redirect from Login page

The plugin has a default login page so you can direct users to it to ask them to log in. If a user logs in using this page they will be automatically redirected to the homepage of the site as per the ` home_url() ` Wordpress function.

You can override this by editing *mw-login-block.php* and putting the following code at the top of the page.

```php
<?php

if($form_parameters['redirect'] == home_url()){
	$form_parameters['redirect'] = 'some other url';
}

?>
```

