Installation
============

This section covers the installation steps of the sofware.

System dependencies
-------------------

You need Poetry installed, see the `install guide <https://python-poetry.org/docs/#installing-with-the-official-installer>`_.

Prerequisites
-------------

You need to have Valkey cloned and installed in the same directory you clone this template in:
this repository and and `valkey` must be in the same directory, and **not** `valkey` cloned in
this directory. 

Valkey
`````

`Valkey <https://valkey.io/>`_: Valkey is an open source (BSD) high-performance key/value datastore 
that supports a variety of workloads such as caching, message queues, and can act as a primary 
database. Valkey can run as either a standalone daemon or in a cluster, with options for replication 
and high availability.

.. note::

    Valkey should be installed from the source, and the repository must be
    in one directory up as the one you will be cloning RansomLook into.

In order to compile and test valkey, you will need a few packages:

.. code-block:: bash

    sudo apt-get update
    sudo apt install build-essential tcl


.. code-block:: bash

    git clone https://github.com/valkey-io/valkey
    cd valkey
    git checkout 7.2
    make
    # Optionally, you can run the tests:
    make test
    cd ..

Clone the source code
---------------------

Clone RansomLook.

.. code-block:: bash

    git clone https://github.com/RansomLook/RansomLook.git

Installation
------------

Install system dependencies (requires root)

.. code-block:: bash

    sudo apt install python3-dev
    sudo apt install libnss3 libnspr4 libatk1.0-0 libatk-bridge2.0-0 libcups2 libxkbcommon0 libxdamage1 libgbm1 libpango-1.0-0 libcairo2 libatspi2.0-0 libxcomposite1 libxfixes3 libxrandr2 libasound2 libwayland-client0 
    sudo apt install libgtk-3-0 libpangocairo-1.0-0 libcairo-gobject2 libgdk-pixbuf2.0-0 libx11-xcb1 libxcursor1
    sudo apt install tor ffmpeg

From the directory you just cloned, run:

.. code-block:: bash

    poetry install

Initialize the `.env` file:

.. code-block:: bash

    echo RANSOMLOOK_HOME="`pwd`" >> .env

Copy the config file:

.. code-block:: bash

    cp config/generic.json.sample config/generic.json

And configure it accordingly to your needs (check configuration page for more details).

Update and launch
~~~~~~~~~~~~~~~~~

Run the following command to fetch the required javascript deps and run RansomLook.

.. code-block:: bash

    poetry run update --yes

With the default configuration, you can access the web interface on `http://0.0.0.0:8000`.
