# Useful Code Snippets

## Using the Core object
As of version 1.0.4 of the base plugin you can now use the ```agora()``` function to quickly grab the core object.

### Example 1
Use directly

```php

<?php

	$x = agora()->user->get_subscriptions();

?>
```
OR

```php
<?php

	$customer = agora()->mw->get_lowest_customer_number_by_email($email_address);

?>
```

### Example 2
Assign to a variable first for re-use:

```php
<?php

  	$core = agora();

 	$core->mw->get_lowest_customer_number_by_email($email_address);

?>
```

## Check Content Access

Sometimes you want to check if a user can access a certain piece of content **before** they're actually viewing it. For example a menu that links to the users subscriptions, or a dashboard showing active subscriptions.

The snippet below will allow you to perform those checks:

```php
<?php
/*
* You could work this into your template to check if a user has access to a page/post before showing a link to it.
* Or you could wrap this in a function and hook into a wordpress filter like the_title, or the_excerpt
*
*
* First check that the class exists 
* This should allow your site to fail gracefully if the plugin is removed
*/
if(class_exists('agora_auth_container')){
  // An auth container gets passed around to all the authentication methods until we know if the user can or can't see the content.
  // The auth container needs to know the post ID to look at.
  $auth_container = new agora_auth_container($post_id);
  
  // The agora_middleware_check_permission filter fires up all the auth methods and passes the container to each one. 
  $auth_container = apply_filters( 'agora_middleware_check_permission', $auth_container);
  
  // When we get the auth container back, we should know if the user can or can't see the content.
  if($auth_container->is_allowed()){
  	// Do stuff... show the content.
  }else{
  	//Don't do stuff...
  }
  
}
?>
```

## Get a list of the users subscriptions
Sometimes you want to know what the user has access to before you have a specific piece of content to check. This code snippet will return a list of authcodes that match the current users middleware data. It will evaluate all rules for the authcodes so you know which are valid.

All authentication methods will be queried so it will evaluate and return all subscriptions, products, AMB, list subscriptions (and any others that hook into the system).

```php
<?php


// $subs will give you a list of all valid subscriptions/product/purchases/list subscriptions etc.
$subs = agora()->authentication->get_user_subscriptions();


?>
```
## Get information about the user
The user class provides lots of getter methods to pull useful information about the user. For example:

```php
<?



// Get the users full name
agora()->user->get_name();

// Get the first name
agora()->user->get_first_name();

// Get the email address
agora()->user->_get_email();

// Get the mailing address
agora()->user->get_address();

?>
```

## Change the plugins default behaviour

Out of the box the Auth plugin will hook into the_content filter and replace the post content with a login box if the user should not be allowed access to a post/page.

However some custom post types, plugins, or page layouts don't work well with this method.

To prevent the default behaviour and implement your own paywall UI, use the following code in the template for your content:

Pay attention at third parameter passed to function: it is priority, it should be the same as defined when the function was originally hooked, otherwise the filter hook won't be removed.

```php
<?php

// Grab the plugin global
global $agora_mw_auth_plugin;

// Remove the filter
remove_filter( 'the_content', array($agora_mw_auth_plugin, 'content_filter'), 999);

// Display the content, now without the filter running. 
the_content();
?>
```

## Customer Self Service

Implement the Customer Self Service by adding the following to your functions.php

```php
<?php
add_shortcode('customer_self_service_iframe', 'css_iframe');

function css_iframe($atts){
	if($url = afps_get_customer_self_service_url($atts['portal'], $atts['base_url'])){
		return '<iframe src="'. $url .'" width="100%" height="800px"></iframe>';
	}else{
		return "<!-- Unable to generate CSS iframe -->";
	}
}

// Customer Self Service URL Builder
function afps_get_customer_self_service_url($portal, $base_url) {

	if(class_exists('agora_core_framework')){
		$core = agora_core_framework::get_instance();
		$customer_id= $core->user->get_customer_number();
		$first = md5($customer_id . $portal);
		$second = md5($first);

		$hash = str_split($second);
		$vid = $hash[4].$hash[7].$hash[11].$hash[18].$hash[26].$hash[31];

		return $base_url.'?cid='.$customer_id.'&prt='.$portal.'&vid='.$vid;

	}
	return false;
}
?>
```

## Foundation site link
Foundation is the successor to Customer Self Service. Use the code below to create a link to foundation:

Add this code to your functions.php

```php
<?php
add_shortcode('foundation_link', 'middleware_foundation_link');

function middleware_foundation_link($atts, $content){
	$url = foundation_url($atts['base_url']);

	return '<a href="'.$url.'">'. $content .'</a>';
}

function foundation_url($url){
	if(class_exists('agora_core_framework')){
		$core = agora_core_framework::get_instance();

		$params = array(
			'z'         => $core->user->get_zip_code(),
			'c'         => $core->user->get_customer_number(),
			't'         => $core->user->get_country(),
			'ReturnURL' =>  'Customers/customeroverview.aspx'
		);
		return $url . '?' . http_build_query($params);
	}
}
?>
```
### Example
```[foundation_link base_url="http://your.foundation.url.com/launchpad.aspx"]Click here for customer self service[/foundation_link]```