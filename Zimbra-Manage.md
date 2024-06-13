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
or
####
    postqueue -p
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
####
Purge specefic Mail Queue:
####
    postsuper -d [message id
####
Purge all Mail Queue:
####
    postsuper -d ALL
####
Find out spaming mail generated users 

#./postqueue -p | awk ‘/sathish@www.sathish.com/ {print $1}’ > /tmp/x
#./postsuper -d  < /tmp/x
####
Identified Compromissed Mail User
####
    cat /var/log/zimbra.log | sed -n 's/.*sasl_username=//p' | sort | uniq -c | sort -n
####
Delete specific user queue mail:<br>
Run this command as Root User 
####
    /opt/zimbra/common/sbin/mailq |grep shakil.hossen@reverie-bd.com|awk {'print $1'} |grep -v @ | tr -d '*!'|/opt/zimbra/common/sbin/postsuper -d -
####
or
####
    /opt/zimbra/common/sbin/postqueue -p | tail -n +2 | awk 'BEGIN { RS = "" } /shakil.hossen@reverie-bd.com/ { print $1 }' | tr -d '*' | /opt/zimbra/common/sbin/postsuper -d -
####
or
####
    /opt/zimbra/common/sbin/postqueue -p |egrep -v '^ *\(|-Queue ID-' | awk 'BEGIN { RS = "" } { if ($7 == "shakil.hossen@reverie-bd.com") print $1} '| tr -d '*!'| /opt/zimbra/common/sbin/postsuper -d -
####
Delete ALL Messages From The deferred queue.
####
    /opt/zimbra/common/sbin/postsuper -d ALL deferred
####
Restart Zimbra Services
####
    zmcontrol restart
####
List all zimbra mail accounts
####
    su - zimbra
####
    zmaccts
####
List all admin accounts
####
    zmprov -l gaaa
####
Set a regular to admin user
####
    zmprov ma sumon@mydomain.com zimbraIsAdminAccount TRUE
####
Remove an admin account to regular account
####
    zmprov ma sumon@mydomain.com zimbraIsAdminAccount FALSE
####
how to locked to unlock zimbra user <br>
Account is closed,inactive for use same command
####
    su - zimbra
####
    zmprov ma username@domain.com zimbraAccountStatus active
####
When a zimbra server unwanted have shutdown or restarted <br>
Then can't be start automatically and show a error message <br>
is zimbra disk volumes hasn't no space
####
<b>Solution:</b>
####
    su zimbra 
    uptime
    zmcontrol status
    df -h
    lsblk
    zmcontrol stop
    zmcontrol start
    zmcontrol status
####
Now Start The Zimber Services.
#
#
Automatic Fixed Problem using zmfixperms
#
####
    /opt/zimbra/libexec/zmfixperms -e -v
####
Check Mailbox Size in Zimbra User
####
su – zimbra
####
zmprov gmi user@maildomain
####
    zmprov gmi sathish@www.sathish.com
####
To check User mailbox Size information in detail. <br>
Show only mailbox size
####
    zmmailbox -z -m testuser@example.com gms 
####
Show Specific Directory Size in Detail
####
    zmmailbox -z -m testuser@example.com gaf 
####
Use This Command For Query All Accounts in My Zimbra Server.
####
    zmprov -l gaa 
####
List all accounts in zimbra. This is additional.
####
    zmaccts
#
Check Audit Log in Zimbra User
#
####
    tail -f /opt/zimbra/log/audit.log | grep user@domain.com
####
Create a new files you can name it <b> allmailboxsize.sh</b> or what ever you wanted then paste this. Make sure you have logged in as zimbra
####
Create a shell file and paste bellow codes.
####
    nano allmailboxsize.sh
####
    #!/bin/bash
    all_account=`zmprov -l gaa`;
    for account in ${all_account}
    do
    mb_size=`zmmailbox -z -m ${account} gms`;
    echo "Mailbox size of ${account} = ${mb_size}";
    done
####
Change the permission and run the script:
####
    chmod 755 allmailboxsize.sh
    ./allmailboxsize.sh
#
#
Get all user's mailbox size from CLI<br>
<i>Reffernce:</i> https://wiki.zimbra.com/wiki/Get_all_user%27s_mailbox_size_from_CLI <br>
just create a new files you can name it checkmailsize.sh or what ever you wanted then paste this
####
Create a shell file and paste bellow codes:
####
nano checkmailsize.sh
####
    #!/bin/bash
    all_account=`zmprov gaa`;
    for account in ${all_account}
    do
    mb_size=`zmmailbox -z -m ${account} gms`;
    echo "Mailbox size of ${account} = ${mb_size}";
    done
####
    su zimbra
####
    chmod 755
####
    ./checkmailsize.sh
#
#
Convert Size to Human-Readable Format:
####
    zmmailbox -z -m user@example.com gms | awk '/mailbox size/{print $3/1024/1024 " MB"}'
####
Managing spam mails in Zimbra mail server <br>
<i>Reffernce:</i>https://sathisharthars.wordpress.com/2013/11/11/managing-spam-mails-in-zimbra-mail-server/
#
Deleting messages from account using the CLI <br>
The bellow command will search a specific folder, Inbox in this case. Displays only messages dated 01/01/2024. Lists up to 50 messages.
####
    zmmailbox -z -m user@domain.com s -t message -l 50 "in:inbox date:01/01/2024"
####
You will see something like the following after issuing the above command:<br>
Id	Type	From	Subject	Date <br>
####
Delete Now Specific msgid 431
####
    zmmailbox -z -m admin@william3.us.zimbralab.com deleteMessage 431
#
####
Restrict Admin View Email <br>
<i>Reffernce:</i>https://wiki.zimbra.com/wiki/Restrict_Admin_%27View_Mail%27

----
