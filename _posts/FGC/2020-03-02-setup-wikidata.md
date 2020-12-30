---
title: Setup Wikidata
---

## Graph Database For Wikidata

- wikidata/mongodb

		/home/cwhsu/workspace/wikidata_filter
		docker attach mongodb
		140.109.19.51:7791
		mongod --host 0.0.0.0:27020
		see client.py

1. Blazegraph
 - `docker pull metaphacts/wikidata-qald-7` (https://hub.docker.com/r/metaphacts/wikidata-qald-7)
 - https://hub.docker.com/r/wikibase/wdqs
2. Neo4j
https://github.com/findie/wikidata-neo4j-importer


### QALD-7 (metaphacts/wikidata-qald-7)

https://hub.docker.com/r/metaphacts/wikidata-qald-7
Docker image holding an installation of the Blazegraph Wikidata query service to support the QALD-7 benchmarking activities. 

#### Requirements
The image requires a populated Blazegraph journal to run. The journal containing a snapshot of Wikidata from 09 Jan 2017 to be used for the QALD-7 Task 4 can be found on the HOBBIT project FTP server: http://hobbitdata.informatik.uni-leipzig.de/qald7/wikidata-en/blazegraph-jnl/blazegraph.jnl (~150GB)