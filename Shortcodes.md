# Shortcodes

The Auth plugin uses a number of shortcodes to achieve various tasks

# agora_middleware_login

The `[agora_middleware_login]` shortcode will add a login box to any page. By default, the plugin creates a page called 'login' when it is activated and place this shortcode on it.. But you are free to use any page for generic logins.

This shortcode does not accept any parameters

# agora_middleware_forgot_password

The `[agora_middleware_forgot_password]` will place a forgot password form on any page it is placed on.

By default the Auth plugin will create a 'forgotpassword' page as a child of the 'login' page and place this shortcode on it.

This shortcode does not accept any parameters



# agora_customer_firstname

The `[agora_customer_firstname]` will place a customer's first name from any page it is placed on.

This shortcode does not accept any parameters

# agora_customer_fullname

The `[agora_customer_fullname]` will place a customer's full name from any page it is placed on.

This shortcode does not accept any parameters

# agora_customer_email

The `[agora_customer_email]` will place a customer's email address from any page it is placed on.

This shortcode does not accept any parameters

# agora_customer_number

The `[agora_customer_number]` will place a customer's number from any page it is placed on.

This shortcode does not accept any parameters

# customer_self_service_iframe

Use this shortcode to add an iframe for the customer self service application.

More information can be found in the [Useful Snippets Page](https://github.com/Pubsvs/Middleware-Authentication/wiki/Useful-Snippets)
This shortcode is not tehnically part of the plugin and should be added to your own functions.php or additional plugin. 

Example: 

```
[customer_self_service_iframe portal="ABC" base_url="https://customerservice.yourdomain.com/csmain.php"]

```

# foundation_link

The `[foundation_link]` shortcode is not part of the plugin but can be implemented by adding the code found in the [Useful Snippets Page](https://github.com/Pubsvs/Middleware-Authentication/wiki/Useful-Snippets)


Example:

```
[foundation_link base_url="https://your.foundation.link"]
```

# hidecontent

The `[hidecontent]` shortcode will hide all of the content within the tag based on the parameters given in the shortcode.  The user must have one of the pubcodes given in the parameters in order to view the content.

Example:

```
[hidecontent pubcodes='abc magazine, abc lifetime']

Protected content.

[/hidecontent]
```