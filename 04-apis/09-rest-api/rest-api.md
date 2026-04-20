# REST API

> The REST server is legacy and will no longer be supported for new development.

- Use the [client API](../03-client-api-clojuredoc/client-api-clojuredoc.md) for new projects
- The REST server will continue to ship with every release of Datomic to support existing projects that use it
- Only critical bug fixes will be made to the REST server going forward

## The REST Service

A Datomic peer can be run as a standalone HTTP service using the *bin/rest* command:

```
bin/rest -p port [-o origins]? [alias uri]+
```

The URIs are as described in the [documentation for connect](../02-peer-api-javadoc/classes/peer/peer.md#connect) with the database name elided, for example:

```
datomic:ddb://aws-region/ddb-table?aws_access_key_id=XXX&aws_secret_key=YYY
```

Clients of the REST API will use the supplied storage aliases to talk about the storages, and will be unaware of the connection and location details. A storage alias is set with the [alias uri] form above, e.g.:

```
dev datomic:dev://localhost:4334/
```

Would alias the local dev storage as "dev" and make it accessible through REST.

Note that the trailing forward slash is required on the storage URI.

The *origins* are a comma-delimited list of allowed origins for Cross-Origin Resource Sharing ([CORS](https://www.w3.org/TR/cors/)) requests. Use `'*'` to allow requests from all origins.

A fully formed launch command for the REST service with the localhost's dev storage available through the alias dev would be:

```
bin/rest -p 8001 dev datomic:dev://localhost:4334/
```

You could then connect to the resulting Datomic REST Service at `https://localhost:8001` and use the storage alias "dev" when required. The examples below use this service with the [Mbrainz 1968-1973 sample](https://github.com/Datomic/mbrainz-sample) database restored.

## Accessing the Service

The REST service can be accessed from a browser or programmatically from a client program.

When accessed from the browser, the service is self-describing and interactive. To access it, simply navigate to the server and port where you started the service, for instance, `https://localhost:8080/` and use the links in the HTML pages to explore the API. The pages contain forms that allow you to invoke all API operations.

For programmatic access, use your preferred HTTP client library to build and submit requests. For POST operations, you can send request bodies in either *application/edn* or *application/x-www-form-urlencoded* format. The service returns data in *application/edn* format by default. You can also use the *Accept* header to specify a desired response format, either *application/edn* or *text/html* (the 'Subscribe to events' operation is an exception to this rule, check below).

Exploring the API in the browser and translating interactions to EDN-based client code is a relatively straightforward process. Specific examples are provided in the section below. Note that query parameters must be URL encoded if required.

## API Operations

This section lists the API operations exposed by the service, including the required URL format and parameters.

### List Aliases

Returns vector of storage aliases the REST service was configured with.

- `GET /data/`

```
curl http://localhost:8001/data/

["dev"]
```

### List Databases

Returns vector of names of databases that exist in the specified storage.

- `GET /data/<storage-alias>/`
  - **Storage-alias**: one of the aliases configured when the REST service was started.

```
curl http://localhost:8001/data/dev/

["mbrainz-1968-1973"]
```

### Create Database

Creates a database in the specified storage, if it does not already exist.

- `POST /data/<storage-alias>/`
  - **Storage-alias**: one of the aliases configured when the REST service was started
  - Request body
  - **Db-name**: the name of the database to create.

| Content Type                        | Body                     |
|-------------------------------------|--------------------------|
| application/edn                     | `{:db-name "new-db-name"}` |
| application/x-www-form-urlencoded   | `db-name=new-db-name`    |

```
curl -X POST -H "Content-Type: application/edn" http://localhost:8001/data/dev/ -d "{:db-name scratch}"

["mbrainz-1968-1973" "scratch"]
```

### Transact

Executes a transaction against the specified database, and returns the result.

- `POST /data/<storage-alias>/<db-name>/`
  - **Storage-alias**: one of the aliases configured when the REST service was started
  - **Db-name**: name of database in storage
  - Request body
  - **Tx-data**: the transaction to execute

| Content Type                        | Body                         |
|-------------------------------------|------------------------------|
| application/edn                     | `{:tx-data [{:db/id "foo" ...}]` |
| application/x-www-form-urlencoded   | `tx-data=[{:db/id "foo" ...}]` |

```
curl -X POST -H "Content-Type: application/edn" http://localhost:8001/data/dev/scratch/ -d "{:tx-data [{:db/doc \"hello REST world\"}]}"

{:db-before {:basis-t 63, :db/alias "dev/scratch"}, :db-after {:basis-t 1000, :db/alias "dev/scratch"},
:tx-data [{:e 13194139534312, :a 50, :v #inst "2014-12-01T15:27:26.632-00:00", :tx 13194139534312, :added true}
{:e 17592186045417, :a 62, :v "hello REST world", :tx 13194139534312, :added true}],
:tempids {-9223350046623220292 17592186045417}}
```

### Retrieve Database Info

Returns information about specified databases.

- `GET /data/<storage-alias>/<db-name>/<basis-t>/`
  - **Storage-alias**: one of the aliases configured when the REST service was started
  - **Db-name**: name of the database in storage
  - **Basis-t**: a t value or `-` for the current database value

```
curl http://localhost:8001/data/dev/mbrainz-1968-1973/-/

{:db/alias "dev/mbrainz-1968-1973", :basis-t 130627}
```

### Datoms

Returns a set or range of datoms from an index.

- `GET /data/<storage-alias>/<db-name>/<basis-t>/datoms?index=<index>[&e=<e>][&a=<a>][&v=<v>][&start=<start>][&end=<end>][&offset=<offset>][&limit=<limit>][&as-of=<as-of-t>][&since=<since-t>][&history=<history>]`
  - **Storage-alias**: one of the aliases configured when the REST service was started
  - **Db-name**: name of database in storage
  - **Basis-t**: a t value or `-` for the current database value
  - **Index**: the index to use, one of aevt, eavt, avet or vaet; one or more leading components of the index can be supplied using e, a and v
  - **E**: an entity id or ident (optional)
  - **A**: an attribute id or ident (optional)
  - **V**: a specific value (optional)
  - **Start**: a starting value for an index range (optional)
  - **End**: an ending value for an index range (optional)
  - **Limit**: number of results to return (optional)
  - **Offset**: first result to return (optional)
  - **As-of-t**: a t value that filters the database to only include facts asserted as of that time (optional)
  - **Since-t**: a t value that filters the database to only include facts asserted since that time (optional)
  - **History**: boolean indicating whether to use a database value containing all historical facts (optional)

```
curl http://localhost:8001/data/dev/mbrainz-1968-1973/-/datoms\?index\=avet\&e\=\&a\=\&v\=\&start\=\&end\=\&offset\=\&limit\=100\&as-
of\=\&since\=

[
 {:e 91, :a 10, :v :abstractRelease/artistCredit, :tx 13194139534313, :added true}
 {:e 64, :a 10, :v :abstractRelease/artists, :tx 1319 4139534312, :added true}
 {:e 78, :a 10, :v :abstractRelease/gid, :tx 13194139534312, :added true}
 {:e 92, :a 10, :v :abstractRelease/name, :tx 13194139534313, :added true}
 ...
]
```

### Entity

Returns all attributes of specified entity.

- `GET /data/<storage-alias/<db-name>/<basis-t>/entity?e=<e>[&as-of=<as-of-t>][&since=<since-t>]`
  - **Storage-alias**: one of the aliases configured when the REST service was started
  - **Db-name**: name of database in storage
  - **Basis-t**: a t value or `-` for the current database value
  - **E**: an entity id or ident
  - **As-of-t**: a t value that filters the database to only include facts asserted as of that time (optional)
  - **since-t**: a t value that filters the database to only include facts asserted since that time (optional)

```
curl http://localhost:8001/data/dev/mbrainz-1968-1973/-/entity\?e\=17592186048482\&as-of\=\&since\=

{:artist/startMonth 2,
:artist/type :artist.type/group,
:artist/startYear 1964,
:artist/sortName "Who, The",
:artist/name "The Who",
:artist/gid #uuid "9fdaa16b-a6c4-4831-b87c-bc9ca8ce7eaa",
:artist/country :country/GB,
:db/id 17592186048482}
```

### Query

Executes a query against one or more databases, and returns results.

- `GET /api/query?q=<query>&args=<args>[&limit=<limit>][&offset=<offset>]`
  - **Query**: query expression
  - **Args**: vector of arguments to query. Each database argument to query requires a map descriptor. Each descriptor must include a value for *:db/alias*. Fields allowed in the descriptor include:
    - **:db/alias**: name of database in storage (required)
    - **:basis-t**: a t value or `-` for current database value (optional)
    - **:as-of**: a t value that filters the database to only include facts asserted as of that time (optional)
    - **:since**: a t value that filters the database to only include facts asserted since that time (optional)
    - **:history**: boolean indicating whether to use a database value containing all historical facts (optional)
  - **Limit**: number of results to return (optional)
  - **Offset**: first result to return (optional)

- `POST /api/query`
  - Request body
  - Same arguments as GET, supports longer list of args

| Content Type                        | Body                              |
|-------------------------------------|-----------------------------------|
| application/edn                     | `{:q [:find ...] :args [{:db/alias ... }]}` |
| application/x-www-form-urlencoded   | `q=[:find ...]&args=[{:db/alias ...}]` |

```
curl "http://localhost:8001/api/query?q=%5B%3Afind+%3Fe+%3Ain+%24+%3Fname+%3Awhere+%5B%3Fe+%3Aartist%2Fname+%3Fname%5D%5D&args=%5B%7B%3Adb%2Falias+%22dev%2Fmbrainz-1968-1973%22%7D+%22The+Who%22%5D&offset=&limit="

[[17592186048482]]
```

### Subscribe to Events

Starts a [Server-Sent Events (SSE)](https://w3c.github.io/eventsource) stream to deliver transaction reports to a client.

- `GET /data/<storage-alias>/<db-name>/<basis-t>/events` (browser access)
  - **Storage-alias**: one of the aliases configured when the REST service was started
  - **Db-name**: name of the database in storage
  - **Basis-t**: a t value or `-` for the current database value

```
http://localhost:8001/data/dev/mbrainz-1968-1973/-/events
```

- `GET /events/<storage-alias/<db-name>` (programmatic access)
  - **Storage-alias**: one of the aliases configured when the REST service was started.
  - **Db-name** - Name of database in storage.

Client must send 'Accept: text/event-stream' header in the GET request, as per the SSE spec. A successful connection returns 200 and leaves the connection open, transmitting heartbeat comment lines and transaction reports.

```
curl -H "Accept: text/event-stream" http://localhost:8001/events/dev/mbrainz-1968-1973
```
