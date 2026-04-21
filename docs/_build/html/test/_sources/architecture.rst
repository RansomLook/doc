Architecture
============

This page describes RansomLook from three complementary angles:

* a **functional view** — what the project does, independent of any technology;
* an **architectural view** — the technical components, available at two zoom levels
  (high-level and ultra-detailed);
* a **user view** — five typical journeys from the perspective of the main personas.

.. contents::
   :local:
   :depth: 2

Functional view
---------------

From upstream sources (leak sites, public leak databases, ransom-notes corpora, crypto
trackers) through the processing pipeline, to the deliverables exposed to operators.

.. mermaid::

    flowchart LR
        %% Sources
        subgraph Sources
            direction TB
            DLS[DLS & forums<br/>via Tor]
            LeakLookup[leak-lookup]
            RF[Recorded Future]
            RansomwhereAPI[Ransomwhe.re]
            TL[ThreatLabz]
            Malpedia[Malpedia]
        end

        %% Pipeline
        subgraph Pipeline [Processing]
            direction TB
            Scrape[Scraping]
            Parse[Parsing &<br/>normalisation]
            Enrich[Enrichment<br/>crypto · leaks · notes<br/>screenshots · aliases]
        end

        %% Deliverables
        subgraph Deliverables
            direction TB
            WebUI[Web UI]
            API[JSON API]
            RSS[RSS feed]
            Notif[Notifications<br/>email · RocketChat · Mastodon]
        end

        DLS & LeakLookup & RF & RansomwhereAPI & TL & Malpedia --> Scrape
        Scrape --> Parse --> Enrich
        Enrich --> WebUI & API & RSS & Notif

        classDef src fill:#1e293b,stroke:#60a5fa,color:#e5e7eb
        classDef proc fill:#164e63,stroke:#22d3ee,color:#e5e7eb
        classDef deliv fill:#14532d,stroke:#10b981,color:#e5e7eb
        class DLS,LeakLookup,RF,RansomwhereAPI,TL,Malpedia src
        class Scrape,Parse,Enrich proc
        class WebUI,API,RSS,Notif deliv

Key points:

* Every source is pulled (not pushed) — RansomLook is a read-only aggregator.
* The pipeline is **idempotent**: re-running scrape/parse on the same input converges
  to the same state.
* The four deliverables share a single data store — the web UI and the API expose
  identical facts.

Architectural view — high-level
-------------------------------

The technical boundaries at a glance.

.. mermaid::

    flowchart TB
        User[User / Browser]
        Web[Flask app<br/>gunicorn]
        Workers[Background CLI workers<br/>scrape · parse · screen · notify · ...]
        Storage[(Valkey<br/>multi-DB)]
        FS[(Filesystem<br/>screenshots · thumbs · logos)]
        Tor{{Tor SOCKS proxy}}
        External[External sources<br/>DLS · forums · leak APIs]

        User -->|HTTPS| Web
        Web --> Storage
        Web --> FS

        Workers --> Storage
        Workers --> FS
        Workers --> Tor --> External

        classDef ui fill:#14532d,stroke:#10b981,color:#e5e7eb
        classDef core fill:#1e3a8a,stroke:#60a5fa,color:#e5e7eb
        classDef store fill:#7c2d12,stroke:#f97316,color:#e5e7eb
        classDef ext fill:#374151,stroke:#9ca3af,color:#e5e7eb
        class User ui
        class Web,Workers core
        class Storage,FS store
        class Tor,External ext

Architectural view — ultra-detailed
-----------------------------------

Every process, data store and external dependency actually present in a running
RansomLook instance.

.. mermaid::

    flowchart TB
        subgraph ClientSide [Client]
            Browser[Browser<br/>EN / FR locale cookie]
        end

        subgraph WebTier [Web tier]
            direction TB
            Proxy[Reverse proxy<br/>nginx - optional]
            Gunicorn[gunicorn]
            Flask[Flask app]
            Babel[Flask-Babel<br/>i18n]
            Login[Flask-Login]
            RestX[Flask-RESTX<br/>/doc Swagger]
            Jinja[Jinja2 + SRI]
        end

        subgraph CLI [Background CLI workers]
            direction TB
            Scrape[poetry run scrape]
            Parse[poetry run parse]
            Screen[poetry run screen]
            NotesW[poetry run notes]
            CryptoW[poetry run cryptocur]
            RFW[poetry run rf]
            NotifyW[poetry run notify /<br/>notifyleak]
            Torrent[poetry run torrent]
            Thumbs[tools/<br/>generate_thumbs.py]
        end

        subgraph ScrapeStack [Scraping stack]
            Lacus[Lacus<br/>capture orchestrator]
            Playwright[Playwright-stealth]
            TorSvc{{Tor daemon}}
        end

        subgraph Valkey [Valkey — multi-DB]
            direction LR
            DB0[DB 0<br/>groups]
            DB3[DB 3<br/>markets]
            DB4[DB 4<br/>posts]
            DB5[DB 5<br/>actors]
            DB6[DB 6<br/>health]
            DB7[DB 7<br/>crypto addrs]
            DBEtc[DB N<br/>alerts · tasks<br/>leaks · RF · notes · sessions]
        end

        subgraph FS [Filesystem - source/]
            Screens[screenshots/]
            ThumbsFS[screenshots/thumbs/]
            Logos[logo/group · market · actor]
            NotesFS[notes dumps]
            RFFS[RF dumps]
        end

        subgraph External [External]
            DLS[DLS / forums<br/>.onion]
            LeakLookup[leak-lookup API]
            RFAPI[RF Identity API]
            RW[Ransomwhe.re]
            TL[ThreatLabz repo]
            Malp[Malpedia]
            SMTP{{SMTP}}
            RCChat{{RocketChat}}
            MastAPI{{Mastodon API}}
        end

        Browser -->|HTTPS| Proxy --> Gunicorn --> Flask
        Flask --> Babel
        Flask --> Login
        Flask --> RestX
        Flask --> Jinja
        Flask --> Valkey
        Flask --> FS

        Scrape --> Lacus --> Playwright --> TorSvc --> DLS
        Scrape --> Valkey
        Parse --> Valkey
        Screen --> FS
        Screen --> Lacus
        NotesW --> TL
        NotesW --> Valkey
        CryptoW --> RW
        CryptoW --> Valkey
        RFW --> RFAPI
        RFW --> FS
        Torrent --> Valkey
        NotifyW --> SMTP
        NotifyW --> RCChat
        NotifyW --> MastAPI
        NotifyW --> Valkey
        Thumbs --> FS

        classDef ui fill:#14532d,stroke:#10b981,color:#e5e7eb
        classDef web fill:#1e3a8a,stroke:#60a5fa,color:#e5e7eb
        classDef worker fill:#4c1d95,stroke:#a855f7,color:#e5e7eb
        classDef store fill:#7c2d12,stroke:#f97316,color:#e5e7eb
        classDef ext fill:#374151,stroke:#9ca3af,color:#e5e7eb
        class Browser ui
        class Proxy,Gunicorn,Flask,Babel,Login,RestX,Jinja web
        class Scrape,Parse,Screen,NotesW,CryptoW,RFW,NotifyW,Torrent,Thumbs,Lacus,Playwright,TorSvc worker
        class DB0,DB3,DB4,DB5,DB6,DB7,DBEtc,Screens,ThumbsFS,Logos,NotesFS,RFFS store
        class DLS,LeakLookup,RFAPI,RW,TL,Malp,SMTP,RCChat,MastAPI ext

Workers run as cron-driven or systemd-timed jobs. They share the Valkey instance
and filesystem but never talk to each other directly — coordination is implicit
through data in Valkey.

User view — five journeys
-------------------------

Typical flows for the five personas documented on :doc:`about <features>`.

SOC / IR — match an observed URL
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Starting from a suspicious URL spotted on an endpoint, confirm whether it is a
known ransomware mirror and take blocking action.

.. mermaid::

    flowchart LR
        S[Suspicious URL<br/>on endpoint] --> A[/urls/]
        A -->|match| B[/group - name/]
        A -->|no match| Z[End]
        B --> C{Mirror status<br/>alive?}
        C -->|yes| D[Export URLs CSV<br/>block at proxy/DNS]
        C -->|no| E[Record in incident]

CTI analyst — weekly trend report
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Build a weekly "state of ransomware" briefing.

.. mermaid::

    flowchart LR
        S[Weekly report] --> A[/stats/]
        A --> B[/hot/]
        B --> C[Pick top<br/>2 trending groups]
        C --> D[/compare/]
        D --> E[Export CSV + PNG]
        E --> F[Briefing doc]

CSIRT / national CERT — constituency triage
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Morning scan for new victims inside the constituency.

.. mermaid::

    flowchart LR
        S[Morning triage] --> A[/recent?window=1/]
        A --> B{Victim in<br/>constituency?}
        B -->|yes| C[/group - name/]
        B -->|no| Z[End]
        C --> D[/notes - group - evidence for notification/]
        C --> E[Subscribe RSS feed<br/>for future mentions]

Security researcher — actor investigation
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Investigate a named actor, trace their relations.

.. mermaid::

    flowchart LR
        S[Study actor X] --> A[/browse?tab=actor/]
        A --> B[/actor - name/]
        B --> C[Relation graph]
        C --> D[Follow peer]
        D --> E[/actor - peer/]
        D --> F[/group - related/]

Integrator — automation & dashboards
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Build a live dashboard or feed data into a SIEM.

.. mermaid::

    flowchart LR
        S[Build dashboard] --> A[/doc - Swagger/]
        A --> B[Programmatic API calls]
        B --> C[Scheduled pull]
        B --> D[/rss.xml subscription/]
        C --> E[Ingest into<br/>SIEM / dashboard]
        D --> E

Editing the diagrams
--------------------

Every diagram is defined inline in this ``.rst`` file using
`Mermaid <https://mermaid.js.org/>`_ syntax (``.. mermaid::`` directive provided
by ``sphinxcontrib-mermaid``). Pushing changes to ``docs/architecture.rst`` is
enough — ReadTheDocs rebuilds automatically.

To preview locally:

.. code-block:: bash

    pip install -r docs/requirements.txt
    sphinx-build -b html docs docs/_build/html
    xdg-open docs/_build/html/architecture.html
