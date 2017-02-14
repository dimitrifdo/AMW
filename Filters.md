# Filters

The Authentication plugin uses a number of filters. 

## agora_middleware_check_permission

Functions that make use of this filter can receive an optional parameter of $post_id

The optional $post_id parameter is used if you want to check if a user has access before you show them something. For example you might have a sidebar that lists content headlines, but you want to hide items that the user hasn't purchased.

The function can contain any rules to permit/deny access.

If access is allowed, it returns true

If access if denied, it returns an object of type WP_Error with a code, and message.

[Click Here](https://github.com/Pubsvs/Middleware-Authentication/wiki/Content-Authentication#the-agora_middleware_check_permission-filter) for more information about using and extending the agora_middleware_check_permission filter

## agora_middleware_admin_menu

Each plugin in the series of Agora Middleware plugins uses this filter to add it's own tabs to the admin area of the plugin suite.

For example
```php
<?php

add_filter( 'agora_middleware_admin_menu', array($this, 'add_tab_item'), 5, 1);

public function add_tab_item($menu){
		$menu[] = array('title' => __('Authentication'), 'page' => $this->plugin_admin_page);
		return $menu;
	}
?>
```
## agora_file_download_access_denied
If the plugin determines that the current user should not be allowed to access a requested file download it will bounce the user to the parent post of the specified file. 

This filter allows you to intercept that URL and send the user somewhere else.

## agora_file_download_home_redirect
When the file download code executes it checks the requested file for a parent post. If no post is found it bounces the user to the homepage. This filter allows you to intercept that.

## before_* language variables
Every time a language variable is loaded a filter is executed allowing you to modify the contents of the variable before it is passed to a template, or output to the browser.

For example:

```php
<?php

    add_filter('before_txt_protected_content', 'my_function');
    
    function my_function($text){
    	// do something with the $text variable.
    	// find/replace some text, change the message, add some formatting etc.
    	return $text;
    }
```

## load_middleware_aggregate_data
Where the agora_middleware_check_permission filter can be used to create an independent authentication system. The load_middleware_aggregate_data filter can be used to extend the rule-based auth system already in place. 

Any function hooking into this filter will receive an object and can add it's own attributes to it. The rule system can be utilised to inspect that data and determine access rights. 

For example, the snippet below will query middleware for any e-mail lists the user is subscribed to and add those lists to the $middleware_data array before returning it.

```php

<?php
	
	add_filter('load_middleware_aggregate_data', array($this, 'load_aggregate_data'), 10, 3);


	public function load_aggregate_data($aggregate_data, $username, $password){
		if(!$this->customer_number){
			$customer = agora()->mw->get_customer_by_login($username, $password);
			$this->customer_number = $customer->customerNumber;
		}

		$listSubscriptions  = $this->core->mw->get_customer_list_signups_by_id($this->customer_number);

		if(is_wp_error($listSubscriptions)){
			return $aggregate_data;
		}elseif(is_wp_error($aggregate_data)){
			$result = new stdClass();
			$result->listSubscriptions = $listSubscriptions;
			return $result;
		}else{
			$aggregate_data->listSubscriptions = $listSubscriptions;
			return $aggregate_data;
		}
	}
?>
```


## agora_mw_auth_field_structure

Functions hooking into this filter will receive an array of $field_structure 

This array tells the Auth plugin what variables can be used for rules as well as where those variables can be found within data added to the users $middleware_data by the *load_middleware_aggregate_data* filter.

See the config file in this plugin for an example of field structure information.

## agora_mw_auth_types

The default auth types are Subscription, Product, and Access Maintenance Billing, future plugins can add their own types.

Functions hooking into this filter will receive an array of $auth_types and can add it's own types to the list before returning it.

## agora_mw_default_rule

Authcodes with no rules associated with them are passed to this filter. Any function can hook into this filter to append it's own rule(s) to the given authcode.

For example if an authcode of type 'subscriptions' has no rules, the following code will add a default rule:

```php
<?php

	add_filter( 'agora_mw_default_rule', 'default_rules', 10, 1);


	function default_rules(agora_authcode $authcode){

		if($authcode->type == 'subscriptions'){
			$authcode->add_rule(new agora_auth_rule(array(
				'field'         => 'circStatus',
				'field_group'   => 'subscriptions',
				'operator'      => 'containedIn',
				'value'         => implode(', ', array('P', 'Q', 'R', 'X', 'W'))
			)));
		}
		return $authcode;
	}
?>
```

## agora_mw_find_purchase

Functions hooking into this filter will receive the users $middleware_data, and the $authcode under inspection. The function can then inspect the middleware data and find the item matching the $authcode->type and the $authcode->advantage_code

For example, if a user has multiple subscriptions there will be an array of subscriptions in the $middleware_data->subscriptionsAndOrders object. The auth plugin itself uses this hook to receive the $middleware_data and $authcode, it will then determine if the user has a subscription object that matches the $authcode->advantage_code 

Once it finds the matching object, it will return it so the rules system can inspect the attributes and compare them with any rules attached to the $authcode

## agora_login_security_die
This filter is fired when the plugin is about to bounce the user to an error page because of too many failed login attempts.

It receives two parameters:

1. $die - boolean
2. $ip - string of the IP address of the user


## agora_middleware_sso_sites
The agora_middleware_sso_sites filter allows you to enable single sign on across partner sites.

Hook into this filter and add the URL of a partner site 

For Example

```php
<?php
add_filter('agora_middleware_sso_sites', 'my_sso_sites');

function my_sso_sites($sites){
	$sites[] = 'http://www.mysite.com/';
      $sites[] = 'http://www.myothersite.com/';
 	return $sites;
}
?>
```

More information can be found [on the Authentication page](https://github.com/Pubsvs/Middleware-Authentication/wiki/Authentication#advanced-single-sign-on)