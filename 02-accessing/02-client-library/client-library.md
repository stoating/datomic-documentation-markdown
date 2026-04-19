# Accessing the Client Library

This page is for users who have completed the [setup](../../01-setup/setup.md) and covers how to integrate the [Datomic client library](../../04-apis/03-client-api-clojuredoc/client-api-clojuredoc.md) into your Java or Clojure project.

## Installing the Client Library

The Datomic client library includes both the synchronous and asynchronous APIs, and is provided via [Maven central](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22client-cloud%22).

> If you are looking to use Client with Datomic Pro, see the [Pro Client Getting Started Tutorial](../../05-operation/01-pro/16-pro-client-getting-started/pro-client-getting-started.md).

### Clojure CLI

To use the Client library from a [Clojure CLI REPL](../../05-operation/02-cloud/11-how-to/how-to.md#clojure-cli), add the following to your [deps.edn](https://clojure.org/guides/deps_and_cli) dependencies map:

```clojure
com.datomic/client-cloud {:mvn/version "1.0.131"}
```

### Maven

To retrieve the Client library for a Maven project, add the following snippet inside the `<dependencies>` block of your pom.xml file:

```xml
<dependency>
 <groupId>com.datomic</groupId>
 <artifactId>client-cloud</artifactId>
 <version>1.0.131</version>
</dependency>
```

### Leiningen

To include the client library in a Leiningen project, add the following snippet to your [project.clj](https://github.com/technomancy/leiningen#configuration) file in the collection under the `:dependencies` key.

```clojure
[com.datomic/client-cloud "1.0.131"]
```

Make sure that Clojure dependency is set to at least `[org.clojure/clojure "1.9.0"]`.

Now that you have the Datomic client library integrated into your project, you can start the [client tutorial](../../03-tutorials/02-client-tutorial/client-tutorial.md).
