# Using Superset

> To use Superset, first follow the steps in the [Python setup](../09-python/python.md) documentation.

[Superset](https://superset.incubator.apache.org) is an open-source business intelligence and data analytics platform that provides numerous statistical and visualization tools. You can configure Superset to use Datomic as a data source via the included *Presto* connector.

## Installing Superset

- Use the venv [created previously](../09-python/python.md#setup-venv). Start the virtual environment using the directory created when making the venv (my-python-env):

```sh
. venv/bin/activate
```

- Follow the [Superset install and setup instructions](https://superset.incubator.apache.org/installation.html#superset-installation-and-initialization) in your Python `venv`, starting with:

```sh
pip install superset
```

- Once installed, launch Superset with:

```sh
superset run -p 8080 --with-threads --reload --debugger
```

- Use `Ctrl-C` to stop the Superset server.

> It is only necessary to run these steps the first time you install Superset.

- To run it again in the future, return to the my-python-env directory and run:

```sh
. venv/bin/activate
superset run -p 8080 --with-threads --reload --debugger
```

- Once Superset is running, you can find it at localhost:8080 in a web browser.

## Using Superset with Datomic Analytics

- In the Superset menu bar, click "Sources" and then "Databases".
- Click the + button to add a new database.
- Choose a name for the DB and enter it in the name field.
- In the SQL Alchemy URI field add the DB URI for the Datomic Analytics system by running the command below. Substitute `host`, `port`, `catalog`, and `schema`:

```sh
presto://<host>:<port>/<catalog>/<schema>
```

- To test the connection, click "Test".
- Check the "Expose in SQL lab" option.
- If desired, configure the remaining options.
- To create the data source, click "Save".
