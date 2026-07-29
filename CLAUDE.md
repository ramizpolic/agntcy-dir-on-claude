## Context

When giving answers, be short and concise, focus on the high-level. Do not overexplain or show debug or investigation notes. If you have already provided the required details, do not repeat them.

## Daemon configuration

For the local node daemon config, use the default values but set the override with the following values:
  - `export DIRECTORY_DAEMON_SERVER_ROUTING_BOOTSTRAP_PEERS="/dns4/routing.ads.outshift.io/tcp/5555/p2p/12D3KooWLf9p3cedc86xGQBaqak6rAFmQk1HxKAK1yh7umHE3amu"` to set the routing bootstrap peer to connect to the AGNTCY network for discovery.
  - `export DIRECTORY_DAEMON_SERVER_ROUTING_REPUBLISH_INTERVAL="1m"` to republish announcements often enough that peers joining during the session converge on the records, instead of the 36h default.
  - `export DIRECTORY_DAEMON_RECONCILER_SCAN_INTERVAL=1s` to set the reconciler scan interval for faster security results.
  - `export DIRECTORY_DAEMON_RECONCILER_SIGNATURE_INTERVAL=1s` to set the reconciler signature interval for faster signature verification results.
  - `export DIRECTORY_DAEMON_RECONCILER_INDEXER_INTERVAL=1s` to set the reconciler indexer interval for faster indexing of records.
  - `export DIRECTORY_DAEMON_RECONCILER_REGSYNC_INTERVAL=1s` to set the reconciler regsync interval for faster registry synchronization.

## Discovery

- When displaying information about discovered results, perform signature and security check with appropriate commands (dirctl pull --scan-result or dirctl verify).
- OASF Extractor is supported and available across local and routing search. When performing taxonomy search (--domain or --skill), a full taxonomy name is required, not just part of it. 
- When asked to search for things on the network, iterate and use high limits for search (--limit 1000). Rely on sync to get the things to your local node. Network search does not support wildcards.
- Before making a sync request, show a discovery matrix as a table of the discovered results, highlighting the important fields (peer address, record CIDs, matched skills, and result count).
- Wait for the sync to complete and give it a few seconds to finish before proceeding to the next step. After sync, show a summary of the synced records and their trust status.
