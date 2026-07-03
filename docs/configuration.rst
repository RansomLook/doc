
Configuration
=============

This section covers the configuration file. We recommand to you to restart the RansomLook after any change in the configuration file.

Entries details
---------------

alertondashboard
^^^^^^^^^^^^^^^^

This entry take the value 'true' or 'false' depenfing if you want to see your alerting keywords on the main page. It's only appears for the last 24 hours.

darkmode
^^^^^^^^

This entry take the value 'true' or 'false' depending if you want to have a black or white interface.

email
^^^^^

This section contains all the information to send the notification to specific users.

.. code-block:: 

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

email_smtp_auth
^^^^^^^^^^^^^^^

This section contains the information to use for an authenticated smtp relais.

ldap
^^^^
This section contains the information to use a LDAP authentication instead of local authentication.

malpedia
^^^^^^^^
Set your malpedia API key in this entry in order to use the script adding description on ransomware.

mastodon
^^^^^^^^
This section contains the information to post new posts on Mastodon

misp
^^^^
This section is used to transmit new posts in an event on MISP.

rf
^^
This section is used to get the new dumps from Recording Future using the API Identity Module.

rocketchat
^^^^^^^^^^
This section contains the information to post new posts on Rocketchat.

siteurl
^^^^^^^
URL used to access to your instance. It's used by the differents modules of the instance.

thread
^^^^^^
Number of threads to use for scraping. You need to adjust the value with your CPU and RAM.

torrent_health
^^^^^^^^^^^^^^
Settings for the BitTorrent swarm health tracker (``poetry run torrent-health``)
and the CIRCL IP enrichment (``poetry run enrich-ips``). All keys are optional;
the defaults below apply when the section is missing.

Retention keys (``*_days``) accept ``0`` to mean "unlimited — never expire".

.. list-table::
   :header-rows: 1
   :widths: 28 12 60

   * - Key
     - Default
     - Description
   * - ``scan_retention_days``
     - ``30``
     - How long each scan entry (with peer sample) is kept in Valkey.
       ``0`` disables the TTL so the history grows unbounded.
   * - ``dead_threshold_days``
     - ``90``
     - Metadata for an infohash with no peer seen for this many days is
       purged at the end of every ``torrent-health`` run. ``0`` disables
       the purge.
   * - ``ip_cache_ttl_days``
     - ``7``
     - How long a CIRCL-enriched IP record (ASN / org / country / PTR) is
       cached. ``0`` makes the cache permanent.
   * - ``scan_duration_seconds``
     - ``45``
     - Seconds the scanner stays in each swarm before snapshotting peers.
       Raise for very large swarms (many handshakes to complete).
   * - ``batch_size``
     - ``10``
     - Number of magnets scanned concurrently within one session window.
   * - ``alive_interval_hours``
     - ``6``
     - Adaptive schedule: minimum interval between scans for a swarm that
       had peers on its last visit.
   * - ``dead_interval_hours``
     - ``24``
     - Same, for swarms that had zero peers recently (< 7 days).
   * - ``frozen_interval_days``
     - ``7``
     - Same, for swarms with no peer observed for more than a week.

twitter
^^^^^^^
This section contains the information to post new posts on Twitter/X using its API.

users
^^^^^
List of local users to use. Doesn't work when LDAP is activated. Password are in cleartext ;D.

website_listen_ip
^^^^^^^^^^^^^^^^^
IP to use by gunicorn. 

website_listen_port
^^^^^^^^^^^^^^^^^^^
Port to use by gunicorn.

Sample of configuration file
----------------------------

.. code-block::

    {                                                                                    
        "email": {                         
            "smtp_server": "localhost",
            "smtp_port": 25,
            "to": ["Team <team@myinstance.local>"],                                                                                                                        [42/314]
            "to_bcc": [],   
            "subject": "New Notification from RansomLook",                     
            "message_head": "Hello,\n\nPlease check the new entries in RansomLook:",
            "message_foot": "Best regards,\n\nRansomlook Team"
        },
        "email_smtp_auth": {
            "auth": false,  
            "smtp_user":"johndoe@myinstance.local", 
            "smtp_pass":"password",
            "smtp_use_starttls": true,
            "verify_certificate": false
        },
        "rocketchat": {        
            "enable": false,
            "server": "localhost",                                                       
            "ssl_verify": false,
            "user_id": "",             
            "auth_token": "",       
            "channel_name": ""    
        },               
        "ldap": {                          
            "enable": false,
            "server": "ldpas://localhost",
            "root_dn": "ou=Users,dc=my,dc=domain,dc=tld",
            "base_dn": "uid",                                                            
            "ssl": true,                                                                 
            "verify": true,                                                              
            "cert": "/path/to/cert.ext"                                                                                                                                       
        },                                                                               
        "twitter": {                                                                     
            "enable": false,                                                                                                                                                  
            "consumer_key": "",              
            "consumer_secret": "",                                                       
            "access_token": "",                                                          
            "access_token_secret": ""                                                    
        },                                   
        "mastodon":{                                                                     
            "enable": false,               
            "url": "",
            "token": ""
        },
        "bluesky":{
            "enable": false,
            "url": "",
            "BLUESKY_HANDLE": "",
            "BLUESKY_APP_PASSWORD":""
        },
        "misp": {
            "enable": false,
            "url": "",
            "apikey": "",
            "tls_verify": true,
            "publish": true
        },
        "users": {"fkz":"fkz","admin":"mypassword"},
        "malpedia": "",
        "rf": "",
        "thread": 32,
        "website_listen_ip": "0.0.0.0",
        "website_listen_port": 8000,
        "alertondashboard": false,
        "darkmode": true,
        "siteurl": "http://myinstance.local",
    }

