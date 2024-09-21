Prerequisites
=============

Software
--------

Generally speaking, requirements are the following:

- A GNU/Linux distribution. Tested on Ubuntu 22.04.3 LTS
- Python version = 3.10. Didn't tested with Python 3.11 and 3.12
- `Valkey <https://github.com/valkey-io/valkey>`_ database
- `Poetry <https://python-poetry.org>`_
- A Tor proxy

Optionally:

- Postfix, or an equivalent software, is required for the email notifications.

For the Web server you can use Apache or Nginx in front of Gunicorn


Hardware
--------


Network
-------

The scraping on the different DLS/Forum/Telegram etc require an Internet connection. 
