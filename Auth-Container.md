The Auth Container is an object that is passed to each authentication method. It stores the following information 

* The ID of content being examined
* A list of authentication methods, and their feedback messages
* Whether the current user has been granted access to that content

If a user is prevented from seeing the content, they are generally shown a login page. The $auth_container object is also passed to this page and could be used to present the user with other options for accessing the content.

## Properties

### post_id

```php
$x = $auth_container->post_id;
```

## Methods

### allow()
Used by an authentication method that has determined if the current user should be allowed to see the content referenced by ->post_id;

#### Example

```php
<?php
	if($foo == $bar){
		return $auth_container->allow();
	}
?>
```

### protected_by()

When an auth function decides the user should not be a allowed to see the current content, it adds a reason to the $auth_container

#### Parameters

##### $auth_type

> *string* (required) A name for the type of authentication being performed. e.g. 'pubcode', 'product', etc.

##### $name

> *string* (required) The name or reference to the specific criteria used to authenticate the user. e.g. a product code, or pubcode


##### $message

> *string* (required) A message that will be displayed to the user to explain why they're not allowed to access the content.

Note, it's best to use a [language variable](https://github.com/Pubsvs/Middleware-Authentication/wiki/Messages-and-Language-Variables) for this.

#### Returns

> *Self* The auth_container object itself is returned.

#### Example

```php
<?php
	if($foo == $bar){
		return $auth_container->allow();
	}else{
		return $auth_container->protected_by('fubar', 'foobar', 'Sorry, but your Foo is not Bar.');
	}
?>
```
### get_protection()

Gets an array of auth methods, messages, and names from the auth_container object.

#### Returns

> *Array*

```php
<?php
	array(
		array(  'type' => 'pubcode',
		 		'name' => 'PUB',
		 		'message' => 'You are not allowed to view this content'
		)
	)
?>
```