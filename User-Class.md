## initialize_user()

This method will take a username and password and make a middleware call to get data about that user. 

It will then attempt to create a new wordpress user with the returned data, filling name, and email fields appropriately.

If a Wordpress account is already in place for the given username, it will update the data in Wordpress with the fresh data from middleware.

This method is mainly used during the login process but can be called at any time.

### Parameters

string $username

string $password

### Returns

array of middleware data equivalent to _get_aggregate_data_by_login_ 

## load_user()

Load user data, loads WP and middleware data into the user object.

If given a user_id parameter will load that user, else it will load the current user

### Parameters

optional int $user_id   A _Wordpress_ user ID

### Returns

object of class WP_User with additional middleware_data properties

## get_login_by_email()

Sends an email address to the MW wrapper, interprets the result and passes back an array of objects containing usernames & passwords

### Parameters

string $email_address

### Returns

array of usernames & Passwords


