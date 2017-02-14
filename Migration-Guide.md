#Migration Guide
This guide provides a very general overview of the process for migrating a site from the old AFPS/Crowd Favorite plugin to the new Publishing Services Middleware plugin.

## You will need
1. SSH or FTP access to your site
2. A copy of the database from your production site
3. The 3 main plugins:
    1. The Base Plugin
    2. The Authentication Plugin
    3. The Debug Plugin
4. Any servers hosting the new plugin *must* have php 5.3.x or greater

## Differences
First off, there are a few key differences in how the two plugins operate...

1. Old Plugin 
    1. The old plugin adds users to the Wordpress database and stores their Advantage customer number as the username
    2. Instead of pulling the users actual email address, it populated the WP database with a fake email address based on their customer number.
    3. Depending on how it was configured sometimes it filled in the name fields
    4. Middleware data was stored as user meta
    5. The uses only pub codes to password protect content.
2. New Plugin
    1. The new plugin stores their Username, in the username field.
    2. Their email address in the email address field
    3. Their name in the name fields
    4. Middleware data is also stored as user meta but there are easier ways to access it through getter methods on the [User Class](https://github.com/ciaranmg/Middleware-Authentication/wiki/User-Class)

    5. The new plugin supports Pub codes (CIR), Products (PRO), and Access Maintenance Billing (AMB)

## Process
Every site is different so the process can vary considerably. Here are some general steps to follow

### Preparation
It's best to set up a copy of your site on a local environment first. It's highly recommended to set up Xdebug so you can add breakpoints, step through code, and inspect variables at runtime. **This is really worth the effort**

### Switching Plugins
1. **Disable** the old Plugins. Including the Debug plugin if it's installed. Don't delete them, you'll need to refer to them later
2. Install the 3 plugins, starting with the Base plugin
    1. Copy the contents of the /theme/ folder in the Authentication plugin into your active Wordpress theme folder
3. Configuration Changes
    1. Authentication Codes
        1. Go to the Wordpress Admin page and under the Middleware 2 menu, click on Authentication
        2. Here you will see the pub codes that were configured by the old plugin
        3. You will notice there are some empty fields.
        4. The *Advantage Code* Field will need to be updated to reflect the pub code for the publication.
        5. In most cases just copying the *Name* field into the *Advantage Code* field will suffice
        6. Ensure the *Type* field is correctly set to Subscription
        7. The *Description* field should be already completed if it was filled in by the old plugin.
    2. Users Table
        1. The user accounts that were created by the old plugin *can* interfere with the new plugin working properly. It is recommended to test the site with the old accounts in place but if you experience problems you may need to delete them.
        2. See The *Warnings* Section below for more information about deleting users
  
### Troubleshooting
Now that the plugins have been switched, the front-end of your site will probably be broken. The following are an example of what you can expect to see. As stated above, every site is different so the specifics will vary.

* You will see multiple php errors about function not defined
* Navigation menus that link to paid content specific to the user will be broken
* Your login boxes and page will look wrong
    * In particular, login boxes embedded in headers & sidebars might not work.
* Sidebar widgets that display user information will be missing or broken.

 Start by fixing the function errors...
 
1. Read the error messages and find out what functions are missing.
2. Search the source code of the old plugins to find the function definitions
3. Examine the code and try to determine what the function was supposed to do
4. Find the equivalent functionality in the new plugin. Read through the rest of this wiki and check the [Useful Snippets](https://github.com/ciaranmg/Middleware-Authentication/wiki/Useful-Snippets) page for help.

For example, If your header has a welcome message for the user, something like this would work:

```php
<?php
// Grab the plugin
$core = agora_core_framework::get_instance();

// Get the users full name
$core->user->get_name();

?>
```
When you have the hard errors taken care of the next steps are look & feel. The new plugin has template files that you copied into your theme. You can customize these in any way you want to make them fit with the design of your site.

### Deploying
Hopefully at this point you have a local copy of your site running, fixed all the bugs and the new plugin is working well.

However now you need to deploy to your production site.

Exact steps for deployment are way beyond the scope of a document like this but bear in mind that once you deploy the code to the server, you will need to repeat the configuration steps that you did within Wordpress. The plugin stores all of it's settings in a database and your production database won't have those settings.

This means that for a brief period while you're changing settings, the production site might not work the way it's supposed to.

If you really can't afford downtime, you might want to export your DB, update it on a local machine, then import it back into production. All while ensuring no new content is added to the site.

## Advanced features
For most sites the standard method of authentication works fine. The plugin hooks into *the_content* filter and checks to see if the user should be allowed to see that piece of content. If they can't it will replace the content with a login box.

Some themes, and particularly sites with advanced post meta will have trouble with this default setup.

Check the [Useful Snippets](https://github.com/ciaranmg/Middleware-Authentication/wiki/Useful-Snippets) page for help getting around these things. You can completely control when and how the login box appears and content is blocked.

## Warnings
As noted above, removing the users created by the old plugin might be necessary, but this should be done with extreme caution.

If your site has any user generated content such as comments, forum posts, reviews, discussions etc. then deleting the user accounts will lose the connection between the content they created and the user account.

There is a WP_CLI command that can help in the conversion process. [Read more about it here](https://github.com/ciaranmg/Middleware-Authentication/wiki/Conversion-command). Please examine it's code carefully before running, and be sure to conduct extensive testing in a staging or local environment before running on a production site.

**Each site is different so a solution needs to be tailored**
