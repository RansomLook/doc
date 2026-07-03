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

    $ poetry run add 'Name of the group' url 0|3

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

Track torrent swarm health
^^^^^^^^^^^^^^^^^^^^^^^^^^

Scans every magnet referenced by a tracked post **or manually attached to a
group** (see :ref:`cli-add-group-torrent` below) and records swarm metrics
(seeders, leechers, peer samples) in ``DB_TORRENT_HEALTH``. The scanner joins
each swarm passively for a few seconds, snapshots connected peers, then
leaves. Peer samples are kept 30 days (TTL), metadata entries are purged
after 90 days of inactivity. IPs listed in ``torrent_health.ignored_ips``
are never recorded.

Intended to run from cron every 6 hours:

.. code-block:: bash

    0 */6 * * * cd /opt/ransomlook/RansomLook && poetry run torrent-health

Ad-hoc invocation and flags:

.. code-block:: bash

    $ poetry run torrent-health                              # full pass (adaptive schedule)
    $ poetry run torrent-health --only <infohash>            # force rescan of one swarm
    $ poetry run torrent-health --group "clop torrents"      # every torrent attached to a group
    $ poetry run torrent-health --group clop --group akira   # multiple groups at once
    $ poetry run torrent-health --magnet "magnet:?xt=..."    # ad-hoc magnet (no group linkage)
    $ poetry run torrent-health --scan-duration 120          # seconds per swarm (default 45)
    $ poetry run torrent-health --max-infohashes 20          # cap for debugging
    $ poetry run torrent-health --log-level DEBUG

``--only`` and ``--group`` both bypass the adaptive interval check and scan
their targets immediately. Without any flag, the scanner uses adaptive
scheduling per infohash: alive swarms are rescanned every 6 hours,
recently-dead swarms every 24 hours, swarms dead for more than a week once
a week. The cron entry above is safe to leave running on 6-hour ticks —
work is skipped automatically when not needed.

All the intervals, retention windows and scan parameters are driven by the
``torrent_health`` section of ``config/generic.json`` — see
:doc:`configuration` for the list of keys.

.. _cli-add-group-torrent:

Attach a torrent to a group without a post
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

When a ransomware group publishes a leak outside the regular DLS pipeline
(Telegram drops, forum posts, private mirrors), you can attach the magnet
or ``.torrent`` file manually. Manually-attached torrents are merged with
post-linked ones by ``collect_magnets()`` and scanned by the next cron
run of ``torrent-health``.

.. code-block:: bash

    $ poetry run tools/add_group_torrent.py --group clop --magnet "magnet:?xt=urn:btih:..."
    $ poetry run tools/add_group_torrent.py --group akira --torrent /path/leak.torrent
    $ poetry run tools/add_group_torrent.py --group clop --from-file magnets.txt
    $ poetry run tools/add_group_torrent.py --remove <infohash>

``--magnet`` and ``--torrent`` can be repeated; all are attached to the
same ``--group``. ``--from-file`` expects one magnet URI or ``.torrent``
path per line, ``#`` lines are ignored.

The same workflow is available from the admin UI at
``/admin/torrent-health/manage`` (single add, bulk add, bulk delete).

Torrent metadata backfill
^^^^^^^^^^^^^^^^^^^^^^^^^

Torrents added from a bare magnet (no ``ws=``, minimal ``tr=``) rely on
libtorrent's ``ut_metadata`` extension (BEP 9) to fetch the info dict
from a willing peer. Under the default 45 s ``scan-duration`` that
exchange often doesn't complete, leaving the meta row with peers but no
name / size / files / trackers. ``torrent-health-backfill`` enumerates
those incomplete rows and rescans them with a longer window (default
300 s) so the metadata can land.

.. code-block:: bash

    $ poetry run torrent-health-backfill                  # live-peer candidates, missing name/trackers
    $ poetry run torrent-health-backfill --dry-run        # preview what would be scanned
    $ poetry run torrent-health-backfill --max 50         # cap per run
    $ poetry run torrent-health-backfill --scan-duration 600   # stubborn swarms
    $ poetry run torrent-health-backfill --include-files  # also fill missing file lists
    $ poetry run torrent-health-backfill --all            # include swarms with 0 peers

By default the tool only targets swarms with ``last_peers_count > 0``:
a dead swarm has nobody to serve the info dict, so rescanning is
unlikely to recover anything. The candidate list is recomputed at every
run, so already-populated rows are skipped automatically — safe to
schedule daily.

Suggested cron (once a day at 01:00, after the nightly ``torrent-health``
pass of the day):

.. code-block:: bash

    0 1 * * * cd /opt/ransomlook/RansomLook && poetry run torrent-health-backfill \
        >> logs/torrent_backfill.log 2>&1

Tracker scrape — BEP-48
^^^^^^^^^^^^^^^^^^^^^^^

Complements ``torrent-health`` for swarms whose tracker is an ``.onion``
(typical of LockBit and other operators running private trackers). The
libtorrent scanner cannot reach those peers because DHT/uTP use UDP,
which does not pass SOCKS5 — so it reports 0 peer even when the swarm
is alive. ``torrent-tracker-scrape`` side-steps that by issuing
standard BEP-48 ``GET /scrape`` calls via Tor and persisting the
numbers the tracker itself reports (seeders / leechers / all-time
downloaded).

.. code-block:: bash

    $ poetry run torrent-tracker-scrape                   # exhaustive pass
    $ poetry run torrent-tracker-scrape --limit 1         # one batch (test)
    $ poetry run torrent-tracker-scrape --clearnet-too    # also scrape clearnet trackers
    $ poetry run torrent-tracker-scrape -v                # DEBUG logging (raw bencode)

Trackers are grouped by scrape URL; up to 64 infohashes are submitted
per request (BEP-48 multi-infohash). If a tracker rejects that, the CLI
falls back to one request per infohash automatically. UDP trackers
(BEP-15) are also supported via a native binary client — they go
clearnet and are skipped under ``only_onion``. Results land in
``meta.tracker_seeders / tracker_leechers / tracker_downloaded /
tracker_history`` and surface on the torrent detail page as a dedicated
KPI banner.

Suggested cron (every 3 hours, single pass covering onion HTTP +
clearnet HTTP + UDP):

.. code-block:: bash

    0 */3 * * * cd /opt/ransomlook/RansomLook && poetry run torrent-tracker-scrape \
        --clearnet-too >> logs/torrent_tracker_scrape.log 2>&1

``--clearnet-too`` adds ~15-30 s to the run but captures the
``tracker_downloaded`` (all-time completions) counter on public UDP
trackers like ``openbittorrent`` and ``opentrackr`` — a metric the
peer scanner does not expose. With the default 30-sample history ring,
a 3-hour cadence yields roughly 3.7 days of trend data.

Config keys live under the ``torrent_tracker_scrape`` section of
``config/generic.json`` — ``only_onion`` (default ``true``) restricts
the scrape to onion-reachable HTTP trackers (clearnet and UDP are left
to the peer scanner, which already announces on them).

Webseed liveness check
^^^^^^^^^^^^^^^^^^^^^^

Sends a ``HEAD`` (fallback one-byte range ``GET`` on 405) to every
unique BEP-19 webseed URL advertised by the tracked torrents — via Tor
for ``.onion`` targets and direct for clearnet. Builds a live map of
the operator's HTTP mirror infrastructure (LockBit publishes 10+
mirrors per leak across distinct onion domains).

.. code-block:: bash

    $ poetry run torrent-webseed-check                    # full pass
    $ poetry run torrent-webseed-check --limit 20         # cap on unique URLs
    $ poetry run torrent-webseed-check --workers 32       # bump concurrency
    $ poetry run torrent-webseed-check --onion-only       # skip clearnet
    $ poetry run torrent-webseed-check -v

Unique URLs are probed once and the result fanned out to every
infohash that advertises them (a single LockBit mirror typically hosts
hundreds of leaks). Results are stored as ``meta.webseed_status`` and
rendered on the detail page as a green / red dot next to each URL.

Suggested cron (every 6 hours):

.. code-block:: bash

    30 */6 * * * cd /opt/ransomlook/RansomLook && poetry run torrent-webseed-check \
        >> logs/torrent_webseed_check.log 2>&1

Config section ``torrent_webseed_check`` in ``config/generic.json`` —
``onion_only`` (default ``false``) keeps clearnet mirrors in scope.

Enrich peer IPs (CIRCL)
^^^^^^^^^^^^^^^^^^^^^^^

Walks every scan stored by ``torrent-health``, extracts the unique peer IPs
and enriches them via the CIRCL services (IP ASN History + BGP Ranking).
Cache hits are skipped; only IPs missing from the cache hit the network.

Complementary to the lazy enrichment performed by the admin UI — running this
cron daily keeps the cache warm so the per-page enrichment in
``/admin/torrent-health/<infohash>`` is instant.

.. code-block:: bash

    $ poetry run enrich-ips                          # incremental
    $ poetry run enrich-ips --force                  # refresh every IP
    $ poetry run enrich-ips --max 500                # cap per run

Suggested cron placement (daily at 03:30, after the last ``torrent-health``
pass of the day):

.. code-block:: bash

    30 3 * * * cd /opt/ransomlook/RansomLook && flock -n /tmp/rl-enrich.lock \
        poetry run enrich-ips >> logs/enrich_ips.log 2>&1

Records are kept in ``DB_TORRENT_HEALTH`` with a TTL controlled by
``torrent_health.ip_cache_ttl_days`` in ``config/generic.json`` (set to 0 to
disable expiration).

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

Get list of databreaches
^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash

    $ poetry run tools/breach.py


Reparse groups
^^^^^^^^^^^^^^

This command reparse files associated to a group and update the screen data in order to be captured during next screen session.

.. code-block:: bash

    $ poetry run tools/getpreviousscreen.py [GroupName]

Import data from Malpedia
^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash

    $ poetry run tools/malpedia.py

Import data from the public instance
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Redownload data from public instance, should be used only for the first populating.

.. code-block:: bash

    $ poetry run tools/import_from_instance


Generate a csv stats file
^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash

    $ poetry run tools/stats.py

Generate screenshot thumbnails
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Pre-generates the JPEG thumbnails served on group/market detail pages. On first
run this can take a while (hundreds of thousands of screenshots); subsequent runs
are incremental and only touch screenshots that are newer than their thumbnail.

.. code-block:: bash

    $ poetry run tools/generate_thumbs.py              # incremental, N-1 CPU workers
    $ poetry run tools/generate_thumbs.py --force      # rebuild every thumb
    $ poetry run tools/generate_thumbs.py --jobs 2     # limit parallelism
    $ poetry run tools/generate_thumbs.py --dry-run    # report only

The script skips ``source/screenshots/old`` and ``source/screenshots/logo``, and
mirrors the source directory tree under ``source/screenshots/thumbs/``.

Regenerate Subresource Integrity (SRI) hashes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Every static JS/CSS asset is referenced in templates with an ``integrity="sha512-..."``
attribute read from ``website/web/sri.txt``. After modifying any static file you must
regenerate this file:

.. code-block:: bash

    $ poetry run tools/generate_sri.py

Failing to do so results in ``KeyError`` 500 errors when Flask-Jinja tries to look up
the hash of an asset that was added but not yet indexed.

Translation catalogues
^^^^^^^^^^^^^^^^^^^^^^

See :doc:`i18n` for the full workflow. Quick reference:

.. code-block:: bash

    $ poetry run pybabel extract -F babel.cfg -o messages.pot .
    $ poetry run pybabel update -i messages.pot -d translations
    $ poetry run pybabel compile -d translations
