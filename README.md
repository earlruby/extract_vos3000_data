extract_vos3000_data
====================

A script that extracts call detail record files (CDRs) from a VOS3000 switch with a MySQL database.

Copyright (C) 2013 Earl C. Ruby III

This program is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; either version 2 of the License, or (at your option) any later version.

This program is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details.

You should have received a copy of the GNU General Public License along with this program; if not, write to the Free Software Foundation, Inc., 59 Temple Place, Suite 330, Boston, MA 02111-1307 USA

The latest version of this code can be downloaded from https://github.com/earlruby/extract_vos3000_data.

Purpose
=======

If you are trying to provide CDR files to a billing service, such as WebCDR.com, you need to provide files containing your latest call data every 15 minutes or so. These scripts create a new CDR file every 15 minutes with the latest CDR records, without interrupting call flow.  You do not need to make any changes to your Asterisk configuration to use these scripts.

Setup
=====

 * Log into your Linux-based switch as root and create a "webcdr" account that will run the script:

   useradd -c 'WebCDR Data Collection' -d /home/webcdr -m -s /bin/bash webcdr

   mkdir ~webcdr/bin

 * From the machine where you downloaded the files, copy the extract_vos3000_data and crontab.txt files to ~webcdr/bin/:

   scp extract_vos3000_data root@[IP of switch]:~webcdr/bin/

   scp crontab.txt root@[IP of switch]:~webcdr/bin/

 * Log into your Linux-based switch as root, su to the webcdr account, and load the crontab:

   su - webcdr

   crontab ~webcdr/bin/crontab.txt

   # If you need to make any changes to the cron
   crontab -e

   exit

 * As root again, change ownership of the webcdr directory back to webcdr:

   chown -R webcdr ~webcdr

The cron job should run every 15 minutes and extract the previous 15 minutes of VOS3000 data to ~webcdr/cdr_data/.

Security
========

For added security, you should create a separate, read-only MySQL account for the webcdr user. Log into MySQL as root and enter:

   MySQL> CREATE USER 'webcdr'@'localhost' IDENTIFIED BY 'password-for-webcdr';
   MySQL> GRANT SELECT ON vos3000db.* TO 'webcdr'@'localhost';

You can pass the user name and password to extract_vos3000_data using --myysql-user and --mysql-password, but anyone running 'ps -ef | grep -i mysql' can see your user name and password while the script is running. A more secure way to run this is to set up a .my.cnf file with your user name and password. A sample file is included with this package. You will need to edit .my.cnf and put in the user name and password for your setup, then install it in the ~webcdr/ directory. Make sure to set the permissions so that no one can read the file:

   chown webcdr ~webcdr/.my.cnf
   chmod 600 ~webcdr/.my.cnf



