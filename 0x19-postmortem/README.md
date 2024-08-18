GET requests on the server led to 500 Internal Server Error

Issue Summary
approximately 12:07 West African Time (WAT) on November 4, 2023,  GET requests on the server led to 500 Internal Server Error, when the expected response was an HTML file defining a simple WordPress site. The WordPress app was encountering a critical error in wp-settings.php when trying to load the file class-wp-locale.phpp. The correct file name, located in the wp-content directory of the application folder, was class-wp-locale.php.

Timeline
. I encountered the issue upon opening the project around 12:07 WAT. I quickly proceeded to find solution to the problem. I checked running processes using ps aux. Two apache2 processes - root and www-data - were properly running.
. I looked in the sites-available folder of the /etc/apache2/ directory, discovered that the web server was serving content located in /var/www/html/. In one terminal, I ran strace on the PID of the root Apache process. In another, I curled the server. Strace gave no useful information.
. I repeated step 2, this time on the PID of the www-data process. Strace revelead an -1 ENOENT (No such file or directory) error occurring upon an attempt to access the file /var/www/html/wp-includes/class-wp-locale.phpp.
Looked through files in the /var/www/html/ directory one-by-one, using Vim pattern matching to try and locate the erroneous .phpp file extension. Located it in the wp-settings.php file. (Line 137, require_once( ABSPATH . WPINC . '/class-wp-locale.php' );).
. At approximately 16:43 WAT, I removed the trailing p from the line then tested another curl on the server. 200 A-ok!
I wrote a Puppet manifest to automate fixing of the error.


Root Cause and Resolution

The root cause of the issue was an erroneous .phpp file extension. Located it in the wp-settings.php file. (Line 137, require_once( ABSPATH . WPINC . '/class-wp-locale.php' );).
I had to trace the line of code in the file and removed the trailing p from the line


Corrective and Preventative Measures
Correction:

Patch involved a simple fix on the typo, removing the trailing p.
Prevention:
This outage was not a web server error, but an application error. To prevent such outages moving forward, please keep the following in mind.
Test! Test test test. Test the application before deploying. This error would have arisen and could have been addressed earlier had the app been tested.
Status monitoring. Enable some uptime-monitoring service such as UptimeRobot to alert instantly upon outage of the website.
