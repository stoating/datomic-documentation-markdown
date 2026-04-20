# Datomic Cloud Override Settings

Datomic Cloud is pre-configured with a set of default settings optimized for general use. However, experienced operators may find it necessary to adjust these settings to better suit their specific requirements. It is imperative to proceed with caution when altering these defaults:

- **Testing**: always test changes in a non-production environment before applying them to your live system.
- **Support consultation**: we highly recommend consulting with [Datomic Support](https://www.datomic.com/support.html) prior to making any modifications. This ensures that changes are made safely and with an understanding of their impact.

Example override settings can be set with the parameter OverrideSettings found in your compute template.

[![override](https://docs.datomic.com/images/override.png)](https://docs.datomic.com/images/override.png)

This parameter is available on primary compute and query group templates. Here is the list of the currently documented Override settings:

| Metric | Default | Description |
|---|---|---|
| pending-ops-limit | 127 | Controls the maximum size of pending operations on a compute node |

`Override Setting Parameter`:

```clojure
{:cluster-node/pending-ops-limit 264}
```
