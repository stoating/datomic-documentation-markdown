# Outer Joins

In the relational database world, outer joins allow you to return relations even when data is missing from one side of a join. For example, you might want all of the [Mbrainz](https://github.com/Datomic/mbrainz-importer#readme) artists and their start years, including artists who do not even have a start year.

In Datomic, you can find the entities you want with [datalog](../../06-reference/03-query-and-pull/02-query-reference/query-reference.md), and then make an independent decision about which details you want to [pull](../../06-reference/03-query-and-pull/03-pull/pull.md). The following example uses a query to find the artists, and then plugs in a pull pattern to get both the artist name and start year:

```clojure
(def find-expr '[:find (pull ?e details-expr)
                 :in $ details-expr
                 :where [?e :artist/name]])
(def details-expr [:artist/name :artist/startYear])
(d/q find-expr db details-expr)
```

The query/pull separation also makes it easy to reuse query and pull logic independently.

Datomic also includes the [get-else query function](../../06-reference/03-query-and-pull/02-query-reference/query-reference.md#get-else), which is closer to a literal outer join in that you can reference a possibly-missing attribute directly in the datalog, specifying an alternate value when the attribute is missing. The example below replaces a missing start year with "Unknown":

```clojure
[:find ?e ?name ?year
 :where
 [?e :artist/name ?name]
 [(get-else $ ?e :artist/startYear "Unknown") ?year]]
```

Check the [full code](https://github.com/cognitect-labs/day-of-datomic-cloud/blob/master/doc-examples/outer_join.clj) for this example in the [Day of Datomic Cloud repo](https://github.com/cognitect-labs/day-of-datomic-cloud#readme).
