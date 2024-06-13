#
# Manage Zimbra Mail Server Using Command Line
#
Check IP Information using command
####
    curl ipinfo.io/ip
####
Switch to Zimbra User
####
    su zimbra
####
Check Status
####
    zmcontrol status
####
Create a single email account: <br>
Syntax:<br>
zmprov ca <email_address> <password>
####
    zmprov ca sumon@mydomain.com 123456
####
Delete Zimbra users from CLI:<br>
Syntax:<br>
zmprov da <email_address><br>
####
    zmprov da sumon@mydomain.com
####
Change user password from CLI
####
    zmprov sp sumon@mydomain.com 1234567%$123Abc
####
Check mail queue in zimbra:
####
    mailq
<br>
or

####
    postqueue -p
<br>
or 

####
    /opt/zimbra/common/sbin/postqueue -p
####
All queue mail re-queue:
####
    postqueue -f
####
or
####
In Zimbra
####
    /opt/zimbra/common/sbin/postsuper -r ALL
####
Purge Mail Queue:
####
    postsuper -d

postsuper -d [message id

postsuper -d ALL

#Find out user generating the spam mail

#./postqueue -p | awk ‘/sathish@www.sathish.com/ {print $1}’ > /tmp/x

#./postsuper -d  < /tmp/x

#identified compromissed mail user

cat /var/log/zimbra.log | sed -n 's/.*sasl_username=//p' | sort | uniq -c | sort -n


#Delete specific user queue mail
#Run this command as Root User. 

/opt/zimbra/common/sbin/mailq |grep shakil.hossen@reverie-bd.com|awk {'print $1'} |grep -v @ | tr -d '*!'|/opt/zimbra/common/sbin/postsuper -d -

#OR

/opt/zimbra/common/sbin/postqueue -p | tail -n +2 | awk 'BEGIN { RS = "" } /shakil.hossen@reverie-bd.com/ { print $1 }' | tr -d '*' | /opt/zimbra/common/sbin/postsuper -d -

#Or

/opt/zimbra/common/sbin/postqueue -p |egrep -v '^ *\(|-Queue ID-' | awk 'BEGIN { RS = "" } { if ($7 == "shakil.hossen@reverie-bd.com") print $1} '| tr -d '*!'| /opt/zimbra/common/sbin/postsuper -d -

#Delete ALL Messages From The deferred queue.

/opt/zimbra/common/sbin/postsuper -d ALL deferred


#restart zimbra services

zmcontrol restart

++++++++++++++++++++++++++++++++++++
+ List all zimbra mail accounts    +
++++++++++++++++++++++++++++++++++++

su - zimbra

zmaccts

#List all admin accounts
zmprov -l gaaa

#Set a regular to admin user
zmprov ma sumon@mydomain.com zimbraIsAdminAccount TRUE

#Remove an admin account to regular account
zmprov ma sumon@mydomain.com zimbraIsAdminAccount FALSE

#how to locked to unlock zimbra user.
#(account is closed,inactive for use same command)

su - zimbra

zmprov ma username@domain.com zimbraAccountStatus active

+++++++++++++++X++++++++++++++++++

#When a zimbra server unwanted have shutdown or restarted.
Then can't be start automatically and show a error message
is zimbra disk volumes hasn't no space.

#Solution:

su zimbra 
uptime
zmcontrol status
df -h
lsblk
zmcontrol stop
zmcontrol start
zmcontrol status

#now start the zimber services.

---------------X----------------------------

++++++++++++++++++++++++++++++++++++++++++++
+ Automatic Fixed Problem using zmfixperms +
++++++++++++++++++++++++++++++++++++++++++++

/opt/zimbra/libexec/zmfixperms -e -v

++++++++++++++++++++++++++++++++++++++
+ Check Mailbox Size in Zimbra User  +
++++++++++++++++++++++++++++++++++++++

su – zimbra

#zmprov gmi user@maildomain

zmprov gmi sathish@www.sathish.com

#To check User mailbox Size information in detail. 
#show only mailbox size

zmmailbox -z -m testuser@example.com gms 

#show specific directory size in detail

zmmailbox -z -m testuser@example.com gaf 

#use this command for query all accounts in my Zimbra server.

zmprov -l gaa 

#List all accounts in zimbra. This is additional.

zmaccts

+++++++++++++++++++++++++++++++++++
+ Check Audit Log in Zimbra User  +
+++++++++++++++++++++++++++++++++++

tail -f /opt/zimbra/log/audit.log | grep user@domain.com

-------------------------------
Create a new files you can name it allmailboxsize.sh or what ever you wanted then paste this. Make sure you have logged in as zimbra

#!/bin/bash
all_account=`zmprov -l gaa`;
for account in ${all_account}
do
    mb_size=`zmmailbox -z -m ${account} gms`;
    echo "Mailbox size of ${account} = ${mb_size}";
done
Change the permission and run the script.
# chmod 755 allmailboxsize.sh
# ./allmailboxsize.sh
-----------------------------------

#Get all user's mailbox size from CLI
https://wiki.zimbra.com/wiki/Get_all_user%27s_mailbox_size_from_CLI

----------------X------------------
#just create a new files you can name it checkmailsize.sh or what ever you wanted then paste this

#!/bin/bash
all_account=`zmprov gaa`;
for account in ${all_account}
do
    mb_size=`zmmailbox -z -m ${account} gms`;
    echo "Mailbox size of ${account} = ${mb_size}";
done
# su zimbra

# vim checkmailsize.sh

# chmod 755

# ./checkmailsize.sh

----------------------X------------------

#Convert Size to Human-Readable Format:

zmmailbox -z -m user@example.com gms | awk '/mailbox size/{print $3/1024/1024 " MB"}'

#Managing spam mails in Zimbra mail server

https://sathisharthars.wordpress.com/2013/11/11/managing-spam-mails-in-zimbra-mail-server/

-----------------X--------------------
#Deleting messages from account using the CLI

#The bellow command will search a specific folder, Inbox in this case. Displays only messages dated 01/01/2024. Lists up to 50 messages.

zmmailbox -z -m user@domain.com s -t message -l 50 "in:inbox date:01/01/2024"

#You will see something like the following after issuing the above command:

Id	Type	From	Subject	Date

#delete now specific msgid 431

zmmailbox -z -m admin@william3.us.zimbralab.com deleteMessage 431


-----------------X--------------------

#Restrict Admin View Email

https://wiki.zimbra.com/wiki/Restrict_Admin_%27View_Mail%27

