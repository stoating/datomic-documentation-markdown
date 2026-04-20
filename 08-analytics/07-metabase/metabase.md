# Using Metabase

[Metabase](https://metabase.com/) is an open-source interactive data analytics, visualization, and business intelligence tool. You can configure Metabase to use Datomic as a data source via the included Presto connector.

## Installing

- Download Metabase from the [Metabase download page](https://www.metabase.com/docs/latest/installation-and-operation/installing-metabase)
- Start Metabase with:

```
java -jar metabase.jar
```

- Point your browser to `localhost:3000`
- Select "Let's get started"
- Fill out "What should we call you"
- Select "I'll add my data later"
- Select your preferred usage data preferences

## Configuring Metabase for Datomic Analytics

- In the Metabase window, click on "Settings" → "Admin" → "Databases" → "Add database"
- Configure the connection to Datomic analytics by running the command below. Substitute appropriate values for `name`, `host`, `port`, `user`, `password` and `catalog`:

```
Database Type: Presto
Name: <give-it-a-name>
Host: <host>
Port: <port>
Database Name: <catalog>
Database Username: <user>
Database Password: <password>
```

Note that:

- `port` is 8989 by default
- The `user` and `password` are your Trino server passwords, which default to `admin` and no password by default
- `catalog` is the [catalog](../03-cloud-configuration/cloud-configuration.md#configure-connections) that you configured when setting up Datomic analytics
