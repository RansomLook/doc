Use in production
=================

If you plan to use in production, we recommand to you to use the following configuration

RansomLook Service
------------------

Edit and copy the service file into system service directory

.. code-block:: bash

    nano etc/systemd/system/ransomlook.service
    sudo cp etc/systemd/system/ransomlook.service /etc/systemd/system/ransomlook.service

Reload the deamon and try to start the service, if all is ok, you can enable it for next reboot

.. code-block:: bash

    sudo systemctl daemon-reload
    sudo systemctl start ransomlook
    sudo systemctl enable ransomlook

Nginx proxy
-----------

Edit and copy the configuration file for nginx

.. code-block:: bash

    nano etc/nginx/sites-available/ransomlook
    sudo cp etc/nginx/sites-available/ransomlook /etc/nginx/sites-available/ransomlook

Test your config and then reload nginx

.. code-block:: bash

    sudo nginx -t
    sudo service nginx reload

Doing the stuff
---------------

In order to automatize the scraping and parsing, we recommand to you to use cron 

.. code-block:: bash

    crontab -e

You can use the following template for grab the data. Recommand to you to adjust the 
differents tasks. Scraping can get many time, so be sure current scraping is over before
starting a new scraping task.

.. code-block::

    0 * * * * cd /opt/ransomlook/RansomLook ; /home/user/.local/bin/poetry run scrape ; \
       /home/user/.local/bin/poetry run parse ; /home/user/.local/bin/poetry run screen ; \
       /home/user/.local/bin/poetry run torrent
    30 * * * * cd /opt/ransomlook/RansomLook ; /home/user/.local/bin/poetry run tools/breach.py ; \
       /home/user/.local/bin/poetry run telegram
    0 1 * * * cd /opt/ransomlook/RansomLook ; /home/user/.local/bin/poetry run cryptocur
    0 2 * * * cd /opt/ransomlook/RansomLook ; /home/user/.local/bin/poetry run notes

In this exemple :

* Scraping is done very hours following by parsing, screenshots of post and then torrent
* Half past every hours breach and telegram account are scraped
* Every days at 1 a.m. cryptocurrencies are updated
* Every days at 2 a.m. ransom notes are updated
