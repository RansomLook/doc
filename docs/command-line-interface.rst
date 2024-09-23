Command Line Interface
======================

Section to explain the various commands available. We suppose that you run them in the installation directory of RansomLook

Core
----

Start the service
^^^^^^^^^^^^^^^^^

.. code-block:: bash

    $ poetry run start

Stop the service
^^^^^^^^^^^^^^^^

.. code-block:: bash

    $ poetry run stop

Update the instance
^^^^^^^^^^^^^^^^^^^
.. code-block:: bash

    $ poetry run update 

Add a group to monitor
^^^^^^^^^^^^^^^^^^^^^^

Select if you want to add a Group (with their DLS) or a forum. DB to use is 0 or 3. 

.. code-block:: bash

    $ poetry run 'Name of the group' url 0|3

Scrape all groups
^^^^^^^^^^^^^^^^^

Please be sure that tor service is installed and running on your instance.

.. code-block:: bash

    $ poetry run scrape

Parse scraped groups
^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash

    $ poetry run parse

Get screenshots for parsed posts
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash

    $ poetry run screen

Retreive Telegram Channels
^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash

    $ poetry run telegram

Retreive cryptocurrencies transactions
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash

    $ poetry run cryptocur

Get the known notes from RansomWare
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash

    $ poetry run notes

Get leaks from Recorded Future
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash

    $ poetry run rf

Get content listing of torrent files
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash

    $ poetry run torrent

Get Tweets
^^^^^^^^^^

.. code-block:: bash

    $ poetry run twitter

Send notification of previous day posts
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash

    $ poetry run notify

Send notification of previous day leaks
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash

    $ poetry run notifyleak

Tools
-----


