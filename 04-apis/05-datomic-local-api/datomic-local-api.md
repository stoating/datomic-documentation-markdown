# Datomic Local API

## Datomic Local API

Most usage of Datomic Local should be via portable Client API calls. Capabilities that are specific to Datomic Local are available through the `datomic.local` namespace:

- [divert-system](#divert-system) to develop and test Datomic Cloud applications against Datomic Local
- [release-db](#release-db) to release [memory used by a database](../datomic-local/datomic-local.md#limits)
- [import-cloud](#import-cloud) to import a Cloud database for Datomic Local use

All Datomic Local API calls take a single arg-map argument.

```clojure
(import-cloud arg-map)
(divert-system arg-map)
(release-db arg-map)
```

### divert-system

Diverts subsequent [d/client](../03-client-api-clojuredoc/client-api-clojuredoc.md#var-client/client) calls for system to local storage. arg-map has the following keys:

| Key | Value | Required |
|---|---|---|
| `:system` | the system to divert | yes |
| `:storage-dir` | overrides `:storage-dir` in `~/.datomic/local.edn` | |

### release-db

Closes the connection to a database and releases the memory used by it. Values of that database can no longer be used. arg-map has the following keys:

| Key | Value | Required |
|---|---|---|
| `:system` | system name | yes |
| `:db-name` | database name | yes |
| `:storage-dir` | overrides `:storage-dir` in `~/.datomic/local.edn` | |

### import-cloud

Import a Datomic Cloud database into a durable Datomic Local database. arg-map has the following keys:

| Key | Value | Required |
|---|---|---|
| `:source` | arg map for (Cloud) d/client merged with arg map for d/connect | yes |
| `:dest` | arg map for (Datomic Local) d/client merged with arg map for d/connect | yes |
| `:filter` | filter spec limits datoms to import | |

A filter spec has the following keys, all optional:

| Key | Value |
|---|---|
| `:before` | t - include only txes before t, exclusive |
| `:since` | t - include only txes after t, inclusive |
| `:include-attrs` | map of attr -> filter |
| `:exclude-attrs` | vector of attrs to be excluded |

`:include-attrs` keys are either fully-qualified attribute names or a namespaced keyword with `*` as a name (includes all attrs in namespace, e.g. `:order/*`).

`:include-attrs` filters are maps with optional `:before` and `:since` keys limiting the time range for attributes as per before/since above.

Schema attributes are always included. When a filter spec is present:

- All other attributes must be included explicitly.
- Excludes take precedence over includes.

You can re-import to get more recent data if you have not transacted locally. You cannot import to a database that currently has an open connection.

The following example imports a customer database, including

- all schema
- all history of customers (except their secrets!)
- orders since May 2020

```clojure
(dl/import-cloud
 {:source {:system "customers"
           :db-name "customers"
           :server-type :ion
           :region "us-east-1"
           :endpoint "https://ljfrt3pr18.execute-api.us-east-1.amazonaws.com"}
  :dest {:system "production-imports"
         :server-type :datomic-local
         :db-name "customers-and-recent-orders"}
  :filter {:include-attrs
           {:customer/* {}
            :order/* {:since #inst "2020-05"}}
           :exclude-attrs [:customer/secret]}})
```
