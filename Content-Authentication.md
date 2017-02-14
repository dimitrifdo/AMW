Content authentication is performed using Wordpress' in-built hooks, and a custom hook for the Auth plugin.

To protect content, the plugin hooks into 'the_content' filter and then calls it's own **agora_middleware_check_permission** filter.

Functions that hook into the **agora_middleware_check_permission** can use any criteria they like to determine if a user can or cannot see a piece of content.

Pub code authentication is included out of the box, and that can be administered through the wordpress back-end.

## The agora_middleware_check_permission filter

Functions that make use of this filter can receive an optional parameter of $post_id

The optional $post_id parameter is used if you want to check if a user has access _before_ you show them something. For example you might have a sidebar that lists content headlines, but you want to hide items that the user hasn't purchased.

The function can contain any rules to permit/deny access.

If access is allowed, it returns true

If access if denied, it returns an object of type WP_Error with a code, and message. 

See below for an example.

## Extending the Authentication Plugin

Say you want to add another method of authentication to your site. 

Users who are named John are pretty important and you want them to have their own area.

In your functions.php, or a custom plugin you can add the following:

```php
<?php
add_filter('agora_middleware_check_permission', 'check_for_john', 1, 1);

function check_for_john($auth_container){
    
    global $current_user;
    get_currentuserinfo();
    if($current_user->user_firstname == 'John'){
        return $auth_container->allow();
    }else{
        return $auth_container->protected_by('namecheck', 'john', 'Sorry, Your name is not John');
    }
}
?>
```
The authentication plugin will take the message you returned, and display it to the user.

It's that simple. Obviously your rules might be more complicated, but it boils down to:

1. Hook into the filter
2. Examine the user
3. Examine the content
4. Decide if the user can see the content, and set the allowed flag. If not, then add a reason to the container and return.

## What is the $auth_container?

The [Auth container](Auth-container) is passed to each auth method, and returned when the auth method has done it's thing.

Since there can be more than one method of authentication e.g. Product, pubcode, etc. There are actually 3 possilbe outcomes from the check permission hook:

1. An authentication method can allow access
If your auth method explicitly allows access, the user will be allowed to see the content regardless of the other protections in place. The allowed flag is set on the $auth_container and passed along.

2. An authentication method can deny access
If your auth method is applied to the current content object, and determines the user is not allowed to see it, your auth method will tell the auth container and pass it along. Note: other auth methods may permit access.

3. An authentication method has no opinion on the current content.
Your auth method is not applied to this content. The $auth_container is returned untouched

