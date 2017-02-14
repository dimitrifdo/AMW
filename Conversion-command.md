This is the heavily-modified code with a few sanity checks and edge cases. 

The approach is to convert as many users as possible and to note any that didn't process and the reason processing didn't take place. We also wanted to process each existing user only once without having to add complicated logic to keep track of progress.

The logic from the conversion code previously provided was used as the basis for the new converter. Initial testing, however showed that the `get_users()` call was skipping users who should have been converted. We switched to `WP_User_Query` which offers more query options, and spent some time honing the query to make sure that all users who should be selected are selected, but only once.

One issue we ran into is that all native user queries require each user to have a role assigned. If this role is not assigned, the user cannot be selected. A large number of users didn't have assigned roles, so a workaround method was written to force WordPress to select all users.

We added a limitation to the search to find only users with '@agora-middleware.com' in their email addresses. We then started the conversion process on a limited number of users to spot edge cases and to add logic to deal with various failures.

# The approach we took with the processing logic is

WordPress users created by the old Middleware plugin had the `user_login` and `user_nicename` set to a numeric Middleware ID. If the `user_login` is not numeric, that user is simply skipped.

The `user_login` and `user_nicename` are then compares for inequality. If they are not equal, but the email is set to '@agora-middleware.com', the user is assumed processed and marked as such.

`user_email` is also double checked for '@agora-middleware.com' and if not found, the user is skipped.

# We also dealt with a few edge cases

A number of instances were encountered where users could not be processed for various reasons. Logic was added to deal with these edge cases gracefully. These edge cases include email addresses that don't contain '@agora-middleware.com' (as a sanity check), users that appear to have already been processed, users that generate errors or no data in the new Middleware plugin, invalid WordPress user names, and duplicate WordPress user names.

Original Middleware plugin users were assigned an email address that was in the form of '[Middleware_ID_number]@agora-middleware.com'. The '@agora-middleware.com' part of the email is used as the basis of the user selection.

In each case where an user with '@agora-middleware.com' could not be processed, the email was then changed to add a status.

For example, an address might be changed from:

- 000000321456@agora-middleware.com

to

- 000000321456@agora-middleware-PROCESSED.com

If the user was converted, but an email address was not detected in the data received from the new Middleware plugin.

# These are the following email changes that are made and the conditions that cause them to be made

- `PROCESSED` - the `user_email` and `user_nicename` fields did not match - user presumed already processed
- `INVALID_USER_NAME` - the new username is not a valid WordPress user name
- `DUPE_USER_NAME` - the new username is already in use in WordPress.
- `UPDATED` - the new Middleware plugin did not provide an email address, marked UPDATED to prevent re-processing
- `ERROR` - the new Middleware plugin returned an error of some sort or was not able to find or process this user.

This is a logical approach since the '@agora-middleware.com' email addresses are fake anyway and not intended to be used. This allowed for a simple and elegant way to differentiate the various processing statuses.

# Database tidy

This will delete all users who do not have buddy-press forum content and allow them to be recreated on first login with the new plugin using middleware data.

```
Delete from wp_users WHERE ID not in (Select distinct post_author from wp_posts where post_type = "topic" or post_type = "reply") AND user_email like "%@agora-middleware.com";
```

# To invoke and use this script

[WP-CLI](http://wp-cli.org/) must be installed on the machine starting the conversion process. You MUST run this from the webserver (vagrant ssh if applicable) and not your local machine. The following line was used during development:

```
wp --url=http://[domain_of_machine] mw-auth convert >> mw-auth-conversion.txt
```

The `--url` parameter may not be necessary, but is required on some setups.

The `mw-auth-conversion.txt` will contain the output for each user processed. The `>>` causes subsequent invocations to append to the output file. A different file name can be used each time the command is run, in which case a single `>` character will suffice.