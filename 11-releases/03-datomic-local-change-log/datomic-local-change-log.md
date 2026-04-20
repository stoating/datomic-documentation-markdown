# Datomic Local Change Log

## Change Log

### 2025/03/19 - 1.0.291

- Fix: `:db-name` key is now available for Datomic Local DBs.
- Fix: changes to [BigDecimal](../../06-reference/01-schema/01-schema-reference/schema-reference.md#dbvaluetype) attribute scale no longer ignored.

### 2024/07/11 - 1.0.285

- Upgraded `com.datomic/client-api` to 1.0.69
- Fix: `datomic.client.api.async/client` now accepts `:datomic-local` as a `:server-type`

### 2024/02/12 - 1.0.277

Fix: correctly deserialize URIs to `java.net.URI`.

### 2023/12/20 - 1.0.276

- Fix: regression introduced in 1.0.267 that could result in a `NoClassDefFoundError` Exception when an exception is encountered in a query.
- Fix: regression introduced in 1.0.267 that could cause a database with blank keyword idents to fail to load.
- Upgrade: Datomic Local now requires an LTS version of Java 11 or greater.
- Upgraded `core.async` to 1.6.681.
- Upgraded `commons-codec` to 1.16.0.
- Upgraded `http-client` to 1.0.126.
- Upgraded `fressian` to 0.6.8.

### 2023/08/16 - 1.0.267

- Changed name to `local` and released under Apache 2.0.
- `:server-type` updated to `datomic-local`.

### 2022/04/06 - 1.0.243

Upgrade Client to 1.0.126.

### 2022/01/10 - 1.0.242

Upgrade Client to 1.0.125.

### 2021/09/27 - 1.0.238

Enhancement: the datalog engine will now do self-unification within a single clause.

### 2021/07/13 - 0.9.235

- Use the latest Client.
- Fix anomaly where Attr-specs for boolean attributes with false values failed.

### 2021/01/20 - 0.9.232

Improvement: enable BigInt fressian handler.

### 2020/11/23 - 0.9.229

- New: change the scale of a [BigDecimal attribute](../../06-reference/01-schema/01-schema-reference/schema-reference.md#dbvaluetype) in a transaction.
- Improvement: better error messages for `import-cloud`.
- Fix: query correctly treats range functions as functions (not as predicates).

### 2020/10/21 - 0.9.225

- New feature [Memdb](../../01-setup/03-local-setup/local-setup.md#memdb).
- Improvement: increase the limit on the total number of datoms in a transaction imported with [`import-cloud`](../../04-apis/05-datomic-local-api/datomic-local-api.md#import-cloud) to 16 million.
- Improvement: increase the limit on the length of strings imported with [`import-cloud`](../../04-apis/05-datomic-local-api/datomic-local-api.md#import-cloud) to 1 million characters.

### 2020/09/25 - 0.9.203

Provide dev-tools via Cognitect Maven repo.

### 2020/07/24 - 0.9.184

Improvement: better error message when calling a function that is not in the `:allow` list of `datomic/ion-config.edn`.

### 2020/07/21 - 0.9.183

Update: Use the latest Client API.

### 2020/07/17 - 0.9.180

- Enhancement: reread the `deps-local.edn` config file when creating a client.
- Enhancement: provide better feedback while loading and importing databases.
- Enhancement: update client documentation about connecting to dev-local systems.

### 2020/07/10 - 0.9.172

Initial release.
