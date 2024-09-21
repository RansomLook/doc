Configuration
=============

This section covers the configuration file.

alertondashboard
~~~~~~~~~~~~~~~~
This entry take the value 'true' or 'false' depenfing if you want to see your alerting keywords on the main page. It's only appears for the last 24 hours.

darkmode
~~~~~~~~
This entry take the value 'true' or 'false' depending if you want to have a black or white interface.

email
~~~~~
This section contains all the information to send the notification to specific users.
```
    "email": {
        "smtp_server": "localhost", # Address for the SMTP server
        "smtp_port": 25, # Port to use
        "from": "RansomLook <ransomlook@myorg.local>", # Email to use
        "to": ["Team <team@myorg.local>"], # Recipients for the mail
        "to_bcc": [], # Hidden recipients for the mail
        "subject": "New Notification from RansomLook", # subject to use
        "message_head": "Hello,\n\nPlease check the new entries in RansomLook:", # Header for your mail
        "message_foot": "Best regards,\n\nRansomlook Team" # Footer for your mail
    }
``` 


email_smtp_auth
~~~~~~~~~~~~~~~
This section contains the information to use for an authenticated smtp relais.

ldap
~~~~
This section contains the information to use a LDAP authentication instead of local authentication.

malpedia
~~~~~~~~
Set your malpedia API key in this entry in order to use the script adding description on ransomware.

mastodon
~~~~~~~~
This section contains the information to post new posts on Mastodon

misp
~~~~
This section is used to transmit new posts in an event on MISP.

rf
~~
This section is used to get the new dumps from Recording Future using the API Identity Module.

rocketchat
~~~~~~~~~~
This section contains the information to post new posts on Rocketchat.

siteurl
~~~~~~~
URL used to access to your instance. It's used by the differents modules of the instance.

thread
~~~~~~
Number of threads to use for scraping.

twitter
~~~~~~~
This section contains the information to post new posts on Twitter/X using its API.

users
~~~~~
List of local users to use. Doesn't work when LDAP is activated. Password are in cleartext ;D.

website_listen_ip
~~~~~~~~~~~~~~~~~
IP to use by gunicorn. 

website_listen_port
~~~~~~~~~~~~~~~~~~~
Port to use by gunicorn.
