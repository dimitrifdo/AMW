# Model Classes

## agora_advantage_item

Base class extended by specific item classes like agora_access_maintenance_billing, and agora_authcode

Constructor accepts an array or stdClass object which will be converted into object attributes.

Optional $authcode parameter accepts an object of type agora_authcode which will be added as an object attribute.

## agora_access_maintenance_billing

Simply extends the base class. Has no distinct features at this time.

## agora_auth_container

A class to contain authentication information.

Authentication Methods are passed an object of this class and they can identify if the user should be allowed to view the content at $post_id 

The object is eventually returned to the Auth plugin which shows/hides the content depending on the final state of the object.

The final object is also passed to the login template to allow affiliates to perform actions based on it.

## agora_auth_rule

An authentication rule describes a field within the users $middleware_data, a value to compare with that field, and an operator to perform the comparision.

For example 'circStatus = R' compares the 'circStatus' variable, with the value 'A' using the 'Equals' operator.

An authcode can have multiple rules. If no rule is attached, a default one will be added to the authcode using the 'agora_mw_default_rule' filter

