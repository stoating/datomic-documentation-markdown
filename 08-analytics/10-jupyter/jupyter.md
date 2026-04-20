# Using Jupyter Notebook

> To start with Jupyter Notebook, first follow the steps in the [Python set up](../09-python/python.md) documentation.

[Jupyter Notebook](https://jupyter.org/) is an interactive web-based notebook frequently used for data analysis and visualization. You can access data stored in Datomic for analysis in Jupyter Notebook (or any Python-based system) using the [PyHive Presto](https://github.com/dropbox/PyHive) library.

## Installing Jupyter

Use the venv [created previously](../09-python/python.md#setup-venv) to perform the steps below.

- From the directory created when making the venv (my-python-env):

```
venv/bin/activate
```

- Install Jupyter by running:

```
pip install jupyterlab
```

## Using Jupyter Notebook with Datomic Analytics

- Start a Jupyter Notebook with the following command:

```
jupyter notebook
```

- Browse to the URL reported by this command
- Create a new Python3 Notebook
- To connect to your Datomic Analytics from Jupyter, use the configuration below:

```python
from pyhive import presto
conn = presto.connect(
    host='<host>',
    port=<port>,
    catalog='<catalog>',
    schema='<schema>',
    username='presto'
)
cur = conn.cursor()
```

- Run a test query:

```python
cur.execute('SELECT * FROM system.runtime.nodes')
print(cur.fetchall())
```
