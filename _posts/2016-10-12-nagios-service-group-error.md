---
toc: true 
toc_label: 'Contents' 
toc_icon: 'cog'
title: Nagios - Service Group Summary ERROR 
category: ['Linux', 'Monitoring', 'Zabbix', 'Nagios', 'Nagiosxi', 'Error']
tags: ['linux', 'monitoring', 'zabbix', 'nagios', 'nagiosxi', 'error']
---
We were working on nagios and found that after our migration, service group summary was not working.
You might get below error on the screen and the solution is similar for both issues. 


### Problem 1.

```
Error is Could not open CGI config file '/usr/local/nagios/etc/cgi.cfg' for reading
```

![Problem 1](https://zubayr.github.io/images/2016-10-12-nagios-service-group-error-problem-1.png)

### Problem 2.

```
Nagios: It appears as though you do not have permission to view information for any of the hosts you requested…
```

![Problem 2](https://zubayr.github.io/images/2016-10-12-nagios-service-group-error-problem-2.png)


### Solution.

Update the `/usr/local/nagios/etc/cgi.cfg` to below configuration, and restart `nagios` service.

``` ruby
# MODIFIED
default_statusmap_layout=6

# UNMODIFIED
action_url_target=_blank
authorized_for_all_host_commands=nagiosadmin
authorized_for_all_hosts=nagiosadmin
authorized_for_all_service_commands=nagiosadmin
authorized_for_all_services=nagiosadmin
authorized_for_configuration_information=nagiosadmin
authorized_for_system_commands=nagiosadmin
authorized_for_system_information=nagiosadmin
default_statuswrl_layout=4
escape_html_tags=1
lock_author_names=1
main_config_file=/usr/local/nagios/etc/nagios.cfg
notes_url_target=_blank
physical_html_path=/usr/local/nagios/share
ping_syntax=/bin/ping -n -U -c 5 $HOSTADDRESS$
refresh_rate=90
show_context_help=0
url_html_path=/nagios
use_authentication=1
use_pending_states=1
use_ssl_authentication=0
```

## Steps to make the above change. 

1. Extract the installation archive.
2. Find the `cgi.cfg` configuration file.
3. Take a backup of the original file.
4. Copy the `cgi.cfg` file to `/usr/local/nagios/etc/cgi.cfg` location.
5. Restart `nagios` services.

### 1. Extract the installation archive.

``` ruby
[root@nagiosserver nagios_download]# tar xvf xi-5.2.9.tar.gz 
[root@nagiosserver nagios_download]# cd nagiosxi/
[root@nagiosserver nagiosxi]# ls
0-repos            5-sudoers        cpan                   get-os-info            install-sourceguardian-extension.sh      rpmupgrade                vmsetup
10-phplimits       6-firewall       dashlets.txt           get-version            install-sudoers                          sourceguardian            wizards.txt
11-sourceguardian  7-sendmail       D-chkconfigalldaemons  init-auditlog          install-templates                        subcomponents             xi-sys.cfg
12-mrtg            8-selinux        debianmods             init-mysql             licenses                                 susemods                  xivar
13-cacti           9-dbbackups      E-importnagiosql       init.sh                nagiosxi                                 tools                     Z-webroot
14-timezone        A-subcomponents  fedoramods             init-xidb              nagiosxi-deps-5.2.9-1.noarch.rpm         ubuntumods
1-prereqs          B-installxi      fix-nagiosadmin        install-2012-prereqs   nagiosxi-deps-el7-5.2.9-1.noarch.rpm     uninstall-crontab-nagios
2-usersgroups      C-cronjobs       F-startdaemons         install-html           nagiosxi-deps-suse11-5.2.9-1.noarch.rpm  uninstall-crontab-root
3-dbservers        CHANGELOG.txt    fullinstall            install-nagiosxi-init  packages                                 upgrade
4-services         components.txt   functions.sh           install-pnptemplates   rpminstall                               verify-prereqs.php
```

### 2. Find the `cgi.cfg` configuration file.

``` ruby
[root@nagiosserver nagiosxi]# find . -name "cgi.cfg" -print
./subcomponents/nagioscore/mods/cfg/cgi.cfg
[root@nagiosserver nagiosxi]# cat ./subcomponents/nagioscore/mods/cfg/cgi.cfg
# MODIFIED
default_statusmap_layout=6

# UNMODIFIED
action_url_target=_blank
authorized_for_all_host_commands=nagiosadmin
authorized_for_all_hosts=nagiosadmin
authorized_for_all_service_commands=nagiosadmin
authorized_for_all_services=nagiosadmin
authorized_for_configuration_information=nagiosadmin
authorized_for_system_commands=nagiosadmin
authorized_for_system_information=nagiosadmin
default_statuswrl_layout=4
escape_html_tags=1
lock_author_names=1
main_config_file=/usr/local/nagios/etc/nagios.cfg
notes_url_target=_blank
physical_html_path=/usr/local/nagios/share
ping_syntax=/bin/ping -n -U -c 5 $HOSTADDRESS$
refresh_rate=90
show_context_help=0
url_html_path=/nagios
use_authentication=1
use_pending_states=1
use_ssl_authentication=0
```

### 3. Take a backup of the original file.

``` ruby
[root@nagiosserver nagiosxi]# cp /usr/local/nagios/etc/cgi.cfg /usr/local/nagios/etc/cgi.cfg.org 
```

### 4. Copy the `cgi.cfg` file to `/usr/local/nagios/etc/cgi.cfg` location.

``` ruby
[root@nagiosserver nagiosxi]# cp ./subcomponents/nagioscore/mods/cfg/cgi.cfg /usr/local/nagios/etc/cgi.cfg 
```

### 5. Restart `nagios` services.

``` ruby
[root@nagiosserver nagiosxi]# service httpd restart; service nagios restart
Stopping httpd:                                            [  OK  ]
Starting httpd:                                            [  OK  ]
Running configuration check...
Stopping nagios:. done.
Starting nagios: done.
```

![Resolved](https://zubayr.github.io/images/2016-10-12-nagios-service-group-error-resolved.png)

## Configuration file with explanation.

Location : [`cgi.cfg.in`](https://sourceforge.net/p/nagios/nagioscore/ci/2a5c6d1f3e0cd48ec6489e188ed7abc321873cfa/tree/sample-config/cgi.cfg.in)

``` ruby
#################################################################
#
# CGI.CFG - Sample CGI Configuration File for Nagios @VERSION@
#
#
#################################################################


# MAIN CONFIGURATION FILE
# This tells the CGIs where to find your main configuration file.
# The CGIs will read the main and host config files for any other
# data they might need.

main_config_file=@sysconfdir@/nagios.cfg



# PHYSICAL HTML PATH
# This is the path where the HTML files for Nagios reside.  This
# value is used to locate the logo images needed by the statusmap
# and statuswrl CGIs.

physical_html_path=@datadir@



# URL HTML PATH
# This is the path portion of the URL that corresponds to the
# physical location of the Nagios HTML files (as defined above).
# This value is used by the CGIs to locate the online documentation
# and graphics.  If you access the Nagios pages with an URL like
# http://www.myhost.com/nagios, this value should be '/nagios'
# (without the quotes).

url_html_path=@htmurl@



# CONTEXT-SENSITIVE HELP
# This option determines whether or not a context-sensitive
# help icon will be displayed for most of the CGIs.
# Values: 0 = disables context-sensitive help
#         1 = enables context-sensitive help

show_context_help=0



# PENDING STATES OPTION
# This option determines what states should be displayed in the web
# interface for hosts/services that have not yet been checked.
# Values: 0 = leave hosts/services that have not been check yet in their original state
#         1 = mark hosts/services that have not been checked yet as PENDING

use_pending_states=1




# AUTHENTICATION USAGE
# This option controls whether or not the CGIs will use any 
# authentication when displaying host and service information, as
# well as committing commands to Nagios for processing.  
#
# Read the HTML documentation to learn how the authorization works!
#
# NOTE: It is a really *bad* idea to disable authorization, unless
# you plan on removing the command CGI (cmd.cgi)!  Failure to do
# so will leave you wide open to kiddies messing with Nagios and
# possibly hitting you with a denial of service attack by filling up
# your drive by continuously writing to your command file!
#
# Setting this value to 0 will cause the CGIs to *not* use
# authentication (bad idea), while any other value will make them
# use the authentication functions (the default).

use_authentication=1




# x509 CERT AUTHENTICATION
# When enabled, this option allows you to use x509 cert (SSL)
# authentication in the CGIs.  This is an advanced option and should
# not be enabled unless you know what you're doing.

use_ssl_authentication=0




# DEFAULT USER
# Setting this variable will define a default user name that can
# access pages without authentication.  This allows people within a
# secure domain (i.e., behind a firewall) to see the current status
# without authenticating.  You may want to use this to avoid basic
# authentication if you are not using a secure server since basic
# authentication transmits passwords in the clear.
#
# Important:  Do not define a default username unless you are
# running a secure web server and are sure that everyone who has
# access to the CGIs has been authenticated in some manner!  If you
# define this variable, anyone who has not authenticated to the web
# server will inherit all rights you assign to this user!
 
#default_user_name=guest



# SYSTEM/PROCESS INFORMATION ACCESS
# This option is a comma-delimited list of all usernames that
# have access to viewing the Nagios process information as
# provided by the Extended Information CGI (extinfo.cgi).  By
# default, *no one* has access to this unless you choose to
# not use authorization.  You may use an asterisk (*) to
# authorize any user who has authenticated to the web server.

authorized_for_system_information=nagiosadmin



# CONFIGURATION INFORMATION ACCESS
# This option is a comma-delimited list of all usernames that
# can view ALL configuration information (hosts, commands, etc).
# By default, users can only view configuration information
# for the hosts and services they are contacts for. You may use
# an asterisk (*) to authorize any user who has authenticated
# to the web server.

authorized_for_configuration_information=nagiosadmin



# SYSTEM/PROCESS COMMAND ACCESS
# This option is a comma-delimited list of all usernames that
# can issue shutdown and restart commands to Nagios via the
# command CGI (cmd.cgi).  Users in this list can also change
# the program mode to active or standby. By default, *no one*
# has access to this unless you choose to not use authorization.
# You may use an asterisk (*) to authorize any user who has
# authenticated to the web server.

authorized_for_system_commands=nagiosadmin



# GLOBAL HOST/SERVICE VIEW ACCESS
# These two options are comma-delimited lists of all usernames that
# can view information for all hosts and services that are being
# monitored.  By default, users can only view information
# for hosts or services that they are contacts for (unless you
# you choose to not use authorization). You may use an asterisk (*)
# to authorize any user who has authenticated to the web server.


authorized_for_all_services=nagiosadmin
authorized_for_all_hosts=nagiosadmin



# GLOBAL HOST/SERVICE COMMAND ACCESS
# These two options are comma-delimited lists of all usernames that
# can issue host or service related commands via the command
# CGI (cmd.cgi) for all hosts and services that are being monitored. 
# By default, users can only issue commands for hosts or services 
# that they are contacts for (unless you you choose to not use 
# authorization).  You may use an asterisk (*) to authorize any
# user who has authenticated to the web server.

authorized_for_all_service_commands=nagiosadmin
authorized_for_all_host_commands=nagiosadmin



# READ-ONLY USERS
# A comma-delimited list of usernames that have read-only rights in
# the CGIs.  This will block any service or host commands normally shown
# on the extinfo CGI pages.  It will also block comments from being shown
# to read-only users.

#authorized_for_read_only=user1,user2




# STATUSMAP BACKGROUND IMAGE
# This option allows you to specify an image to be used as a 
# background in the statusmap CGI.  It is assumed that the image
# resides in the HTML images path (i.e. /usr/local/nagios/share/images).
# This path is automatically determined by appending "/images"
# to the path specified by the 'physical_html_path' directive.
# Note:  The image file may be in GIF, PNG, JPEG, or GD2 format.
# However, I recommend that you convert your image to GD2 format
# (uncompressed), as this will cause less CPU load when the CGI
# generates the image.

#statusmap_background_image=smbackground.gd2




# STATUSMAP TRANSPARENCY INDEX COLOR
# These options set the r,g,b values of the background color used the statusmap CGI,
# so normal browsers that can't show real png transparency set the desired color as
# a background color instead (to make it look pretty).  
# Defaults to white: (R,G,B) = (255,255,255).

#color_transparency_index_r=255
#color_transparency_index_g=255
#color_transparency_index_b=255




# DEFAULT STATUSMAP LAYOUT METHOD
# This option allows you to specify the default layout method
# the statusmap CGI should use for drawing hosts.  If you do
# not use this option, the default is to use user-defined
# coordinates.  Valid options are as follows:
#	0 = User-defined coordinates
#	1 = Depth layers
#       2 = Collapsed tree
#       3 = Balanced tree
#       4 = Circular
#       5 = Circular (Marked Up)

default_statusmap_layout=5



# DEFAULT STATUSWRL LAYOUT METHOD
# This option allows you to specify the default layout method
# the statuswrl (VRML) CGI should use for drawing hosts.  If you
# do not use this option, the default is to use user-defined
# coordinates.  Valid options are as follows:
#	0 = User-defined coordinates
#       2 = Collapsed tree
#       3 = Balanced tree
#       4 = Circular

default_statuswrl_layout=4



# STATUSWRL INCLUDE
# This option allows you to include your own objects in the 
# generated VRML world.  It is assumed that the file
# resides in the HTML path (i.e. /usr/local/nagios/share).

#statuswrl_include=myworld.wrl



# PING SYNTAX
# This option determines what syntax should be used when
# attempting to ping a host from the WAP interface (using
# the statuswml CGI.  You must include the full path to
# the ping binary, along with all required options.  The
# $HOSTADDRESS$ macro is substituted with the address of
# the host before the command is executed.
# Please note that the syntax for the ping binary is
# notorious for being different on virtually ever *NIX
# OS and distribution, so you may have to tweak this to
# work on your system.

ping_syntax=/bin/ping -n -U -c 5 $HOSTADDRESS$



# REFRESH RATE
# This option allows you to specify the refresh rate in seconds
# of various CGIs (status, statusmap, extinfo, and outages).  

refresh_rate=90

# DEFAULT PAGE LIMIT
# This option allows you to specify the default number of results 
# displayed on the status.cgi.  This number can be adjusted from
# within the UI after the initial page load. Setting this to 0
# will show all results.  

result_limit=100


# ESCAPE HTML TAGS
# This option determines whether HTML tags in host and service
# status output is escaped in the web interface.  If enabled,
# your plugin output will not be able to contain clickable links.

escape_html_tags=1




# SOUND OPTIONS
# These options allow you to specify an optional audio file
# that should be played in your browser window when there are
# problems on the network.  The audio files are used only in
# the status CGI.  Only the sound for the most critical problem
# will be played.  Order of importance (higher to lower) is as
# follows: unreachable hosts, down hosts, critical services,
# warning services, and unknown services. If there are no
# visible problems, the sound file optionally specified by
# 'normal_sound' variable will be played.
#
#
# <varname>=<sound_file>
#
# Note: All audio files must be placed in the /media subdirectory
# under the HTML path (i.e. /usr/local/nagios/share/media/).

#host_unreachable_sound=hostdown.wav
#host_down_sound=hostdown.wav
#service_critical_sound=critical.wav
#service_warning_sound=warning.wav
#service_unknown_sound=warning.wav
#normal_sound=noproblem.wav



# URL TARGET FRAMES
# These options determine the target frames in which notes and 
# action URLs will open.

action_url_target=_blank
notes_url_target=_blank




# LOCK AUTHOR NAMES OPTION
# This option determines whether users can change the author name 
# when submitting comments, scheduling downtime.  If disabled, the 
# author names will be locked into their contact name, as defined in Nagios.
# Values: 0 = allow editing author names
#         1 = lock author names (disallow editing)

lock_author_names=1




# SPLUNK INTEGRATION OPTIONS
# These options allow you to enable integration with Splunk
# in the web interface.  If enabled, you'll be presented with
# "Splunk It" links in various places in the CGIs (log file,
# alert history, host/service detail, etc).  Useful if you're
# trying to research why a particular problem occurred.
# For more information on Splunk, visit http://www.splunk.com/

# This option determines whether the Splunk integration is enabled
# Values: 0 = disable Splunk integration
#         1 = enable Splunk integration

#enable_splunk_integration=1


# This option should be the URL used to access your instance of Splunk

#splunk_url=http://127.0.0.1:8000/




# NAVIGATION BAR SEARCH OPTIONS
# The following options allow to configure the navbar search. Default
# is to search for hostnames. With enabled navbar_search_for_addresses,
# the navbar search queries IP addresses as well. It's also possible
# to enable search for aliases by setting navbar_search_for_aliases=1.

navbar_search_for_addresses=1
navbar_search_for_aliases=1
```