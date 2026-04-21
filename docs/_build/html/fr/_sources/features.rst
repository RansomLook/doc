Web interface
=============

This page gives an overview of every public-facing page of a RansomLook instance.
All pages are available on the official public instance at https://www.ransomlook.io.

Navigation is organised in three groups in the sidebar:

* **Data** — Recent, Browse, Trending, Stats
* **Intel** — URLs, Crypto, Leaks, Notes, RF Dumps
* **Info** — API, Glossary, About

Homepage (``/``)
----------------

.. image:: _static/img/screenshots/home.png
    :alt: RansomLook homepage
    :width: 100%

The landing page summarises live activity:

* total ransomware posts tracked,
* last 7-day headline metrics with delta vs the previous week,
* *Top movers* (groups with the biggest weekly swing) and *Latest posts*,
* a 30-day sparkline of the overall posting activity,
* CTAs towards :ref:`/recent <page-recent>`, :ref:`/stats <page-stats>` and the API docs.

.. _page-recent:

Recent posts (``/recent``)
--------------------------

.. image:: _static/img/screenshots/recent.png
    :alt: Recent posts page
    :width: 100%

Flat chronological listing of victim posts across every tracked group.

* **Time window selector** — ``1 / 3 / 7 / 30 days``. Default window: 3 days.
* Hard cap at 2000 rows per window. When exceeded, a notice shows how many rows were trimmed.
* Live filters: free-text search (title + group name) and a per-group dropdown.
* 24-hour sparkline of posting activity in the hero.

.. _page-browse:

Browse (``/browse``)
--------------------

.. image:: _static/img/screenshots/browse.png
    :alt: Browse entities page
    :width: 100%

Card grid of every tracked entity — ransomware groups, markets/forums and threat actors.

* Tabs to switch between *All / Groups / Markets / Actors*.
* Health chips (*Healthy / Degraded / Offline*) filter entities by mirror availability.
* Alphabet bar (A-Z / 0-9 / #) and live name/alias search.
* Each card exposes parser status, private flag, wanted-listing flag, alias count.

.. _page-urls:

URLs (``/urls``)
----------------

.. image:: _static/img/screenshots/urls.png
    :alt: URL inventory page
    :width: 100%

Flat view of every tracked mirror URL for groups and markets. Useful for IR teams
checking if an observed URL matches a tracked mirror.

* Filters: entity type (*All / Groups / Markets*), availability (*All / Online / Offline*),
  live text search matching both entity name and URL substring.
* One row per mirror — shows the entity, the full URL, live status, last scrape date,
  30-day uptime and a 30-day health sparkline.
* **CSV export** (``/urls.csv``) respects the active filters.
* Respects privacy: private entities and private URLs are hidden to anonymous visitors.

.. _page-hot:

Trending (``/hot``)
-------------------

.. image:: _static/img/screenshots/hot.png
    :alt: Trending groups page
    :width: 100%

Ranks groups by posting activity over a configurable window (1/3/7/14/30/90 days).

* Sort modes: *Trending (Δ%)*, *Volume*, *New entrants*.
* CSV export of the ranked list.
* Per-row sparkline showing the daily post distribution inside the window.

.. _page-stats:

Stats (``/stats``)
------------------

.. image:: _static/img/screenshots/stats.png
    :alt: Interactive statistics page
    :width: 100%

Interactive charts (Plotly) covering posts across time.

* Controls: time period (7/14/30/90 days or custom range), hourly/daily/weekly aggregation,
  optional rolling average (3/7/14), Top-N group filter (5/10/20/all), percentage normalisation.
* Three charts: Top-N bar, stacked area, timeline scatter. PNG export available on each.
* CSV export of the filtered dataset, share URL to reproduce a given view.

.. _page-compare:

Compare (``/compare``)
----------------------

.. image:: _static/img/screenshots/compare.png
    :alt: Compare two entities
    :width: 100%

Side-by-side comparison of two entities (groups or markets/forums).

* Posts over 7d/30d/365d, mirrors up, 30-day uptime, Captcha and RaaS flags.
* Live charts: post volume bars + mirror availability gauges.

.. _page-crypto:

Crypto (``/crypto``)
--------------------

.. image:: _static/img/screenshots/crypto.png
    :alt: Cryptocurrencies by group
    :width: 100%

Cryptocurrency wallets grouped by actor.

* Chip grid with live filter by name / alias.
* Per-group detail page (``/crypto/<name>``) lists observed wallets by blockchain, each with
  their transactions, filter controls (date range, min/max amount, direction) and Breadcrumbs
  integration for supported chains.
* CSV export per wallet and per group.

.. _page-leaks:

Leaks (``/leaks``)
------------------

.. image:: _static/img/screenshots/leaks.png
    :alt: Leaks listing page
    :width: 100%

Public leak databases tracked for enrichment. Search by name/description/URL/title.
Each entry opens a modal with size, record count, indexing date and schema columns.

.. _page-notes:

Notes (``/notes``)
------------------

.. image:: _static/img/screenshots/notes.png
    :alt: Ransomware notes by group
    :width: 100%

Ransom note samples grouped by actor. Detail page (``/notes/<group>``) lists every note
with content and a *Copy* action.

.. _page-rf:

RF Dumps (``/RF``)
------------------

Optional Recorded Future integration. Available when an API key is configured in
``config/generic.json``. Lists retrieved dumps with description and download date; modal
view shows breach metadata.

Entity detail pages
-------------------

Group (``/group/<name>``)
~~~~~~~~~~~~~~~~~~~~~~~~~

* Hero with logo, quick stats (total posts, posts over 7d/30d, 30-day uptime average).
* Activity sparkline (30 days), Plotly time chart.
* Contact details (PGP, Matrix, Telegram, Tox, Session, Simplex, Max, Sonar, mail, Jabber).
* Mirror listings split by role (URLs / File servers / Chat / Admin) each showing status,
  screenshot thumb, 30-day uptime and a health sparkline.
* Affiliates list (linked to actor pages when known).
* CSV export for URLs and posts.

Market / Forum (``/market/<name>``)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Same layout as group pages but sourced from the market database.

Actor (``/actor/<name>``)
~~~~~~~~~~~~~~~~~~~~~~~~~

* Hero with aliases, *WANTED* banner linking to FBI / Europol / INTERPOL notices when applicable.
* Relation graph (cytoscape) showing linked groups, markets and peer actors.
* Personal information, contacts, sources and image gallery with lightbox.

Glossary (``/glossary``)
------------------------

.. image:: _static/img/screenshots/glossary.png
    :alt: Glossary page
    :width: 100%

Reference page defining terminology used throughout RansomLook: groups, actors,
infrastructure types (DLS, FS, Chat, Admin, Relay), data metrics, ecosystem and relationships.

About (``/about``)
------------------

.. image:: _static/img/screenshots/about.png
    :alt: About page
    :width: 100%

Project overview with sections:

* What is RansomLook / main components
* Who uses RansomLook (SOC, CTI, CSIRT, research, journalism)
* Getting started (entry-point per use case)
* Data freshness policy
* License (AGPL v3), API license (CC BY 4.0)
* Credits and external data sources

Feeds and automation
--------------------

RSS feed
~~~~~~~~

A ``/rss.xml`` feed exposes the most recent posts. The sidebar footer exposes a
direct link next to the social icons.

API
~~~

Swagger documentation is served at ``/doc/``. See :doc:`api` for details.

Language switcher
-----------------

Every page can be rendered in English or French. The switcher lives in the top bar
(``EN / FR``). The active locale is persisted in a cookie (``locale``) for one year.
When no cookie is set, the locale is picked from the browser's ``Accept-Language`` header,
defaulting to English.

See :doc:`i18n` for contributing new translations.
