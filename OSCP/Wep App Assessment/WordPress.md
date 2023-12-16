
Ensure to check the web technology.  If it is word press utilize wpscan.

#### WPscan

`wpscan -u url` - scan the WordPress website for vuln and other information.

`plugins` - search the plugins of the wpscan output on google for vulnerabilities.


#### Creating plugin for backdoor

add the following to the top of a php file.

This will be the header of out plugin for out plugin meta data.

```
<?php
/*
Plugin Name:  WPBeginner Plugin Tutorial
Plugin URI:   https://www.wpbeginner.com
Description:  A short little description of the plugin. It will be displayed on the Plugins page in WordPress admin area.
Version:      1.0
Author:       WPBeginner
Author URI:   https://www.wpbeginner.com
License:      GPL2
License URI:  https://www.gnu.org/licenses/gpl-2.0.html
Text Domain:  wpb-tutorial
Domain Path:  /languages
*/
```

concatenate the file with `/usr/share/webshells/php/php-reverse-shell.php`

remove the extra comments and the leading `<?php` from the reverse shell file.

name the file the same as the zip file name e.g. `phpplugin.php = phpplugin.zip`

ONLY ZIP THE FILE -NOT A FOLDER

