RansomLook
====================


.. only:: html

    .. image:: https://img.shields.io/github/v/release/RansomLook/RansomLook.svg?style=flat-square
        :alt: Latest release

    .. image:: https://img.shields.io/github/license/RansomLook/RansomLook.svg?style=flat-square
        :alt: License

    .. image:: https://img.shields.io/github/stars/RansomLook/RansomLook.svg?style=flat-square
        :alt: Stars

    .. image:: https://readthedocs.org/projects/ransomlook/badge/?version=latest
        :alt: Documentation Status

.. toctree::
    :caption: Technical considerations
    :maxdepth: 3
    :hidden:

    prerequisites
    installation
    configuration
    update
    command-line-interface
    production

.. toctree::
    :caption: Web interface
    :maxdepth: 3
    :hidden:

    features
    architecture
    i18n

.. toctree::
    :caption: Administration
    :maxdepth: 3
    :hidden:

    admin

.. toctree::
    :caption: API
    :maxdepth: 3
    :hidden:

    api


Presentation
------------

RansomLook is an open-source project aimed at assisting users in tracking ransomware-related posts and activities across various sites, forums, and Telegram channels. 

A public instance operated by RansomLook Team is available at https://ransomlook.io.

Features
~~~~~~~~

Data collection:

* Blog / DLS monitoring with per-group parsers for victim extraction
* Forum monitoring (listing only — parsing is not available)
* Ransom notes indexing from ThreatLabz
* Public leak tracking (leak-lookup) and optional Recorded Future dumps (API key required)
* Monitoring of known cryptocurrency wallets

Web interface (see :doc:`features` for details):

* ``/urls`` — flat inventory of every tracked mirror with status, 30-day health, CSV export
* ``/browse`` — card grid of groups, markets and threat actors with health and search filters
* ``/recent`` — chronological posts over 1/3/7/30-day windows
* ``/hot`` — trending groups (Δ%, volume, new entrants) with sparklines
* ``/stats`` — interactive Plotly charts with rolling averages and Top-N filtering
* ``/compare`` — side-by-side comparison of two entities
* ``/crypto`` — wallets by group with transactions and Breadcrumbs integration
* Entity detail pages for groups, markets and threat actors with relation graphs

Automation / integration:

* JSON API documented via Swagger at ``/doc/``
* RSS feed of recent posts at ``/rss.xml``
* Optional notifications (RocketChat, e-mail)
* Optional Mastodon bridge

i18n:

* English and French UI, switchable via top-bar toggle (cookie-based); see :doc:`i18n`


Is it free?
~~~~~~~~~~~

Yes, it is free, and more importantly, it is open-source.

How can I follow new posts?
~~~~~~~~~~~~~~~~~~~~~~~~~~~

There are various ways to stay updated with new posts:

* By running your own instance and enabling RocketChat and/or email notifications
* By checking the public instance: https://www.ransomlook.io
* By accessing the API: https://www.ransomlook.io/doc
* By subscribing to the RSS feed: https://www.ransomlook.io/rss.xml
* By following us on Mastodon: `@Ransomlook@social.circl.lu <https://social.circl.lu/@Ransomlook>`_
* By following us on BlueSky: `ransomlook.bsky.social <https://bsky.app/profile/ransomlook.bsky.social>`_

There is NO official Telegram Channel!

Want to Be Part of the RansomLook Community?
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Join us in making RansomLook even better! Here's how you can contribute:

* Create an issue on our GitHub repository.
* Submit your pull requests with new features or improvements.
* Share new sources of DLS (Darknet Leak Sites) with us.
* Report any bugs you come across.
* Suggest exciting new functionalities that could enhance RansomLook.

Credits & Thanks
~~~~~~~~~~~~~~~~

RansomLook is maintained by Alexandre Dulaunoy (https://github.com/adulau/) and Fafner [_KeyZee_] (https://github.com/fafnerkeyzee).

We thank Tammy Harper for her contributions to adding new groups and her regular feedback to improve the project.

The code is based on RansomWatch.

External data are from:

* Malpedia for description
* Ransomwhe.re for cryptoCurrency
* threatlabz for the RansomNotes
* leak-lookup for the public leaks

Contact
-------

Ping us on Twitter or Mastodon, or you can directly contact us via issue on Github.
You can also contact us by using: contact [at] ransomlook [dot] io

License
-------

RansomLook is licensed under
`GNU Affero General Public License version 3 <https://www.gnu.org/licenses/agpl-3.0.html>`_.
