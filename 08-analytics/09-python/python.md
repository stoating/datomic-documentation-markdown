# Setting Up Python

The Datomic team recommends using a Python *virtualenv* for any Python-based analytics tools.

Python 3 ships with virtualenv by default, however, if it is missing in your environment
you can install it with your package manager or using pip:

```
pip3 install virtualenv
```

## Setting up a Python Virtual Environment

- Create a new empty directory for your Python environment and cd into it:

```sh
mkdir my-python-env
cd my-python-env
```

- If you do not yet have it, install Python 3 locally:

```sh
brew install python3
```

- Set up a Python virtual environment:

> This step is only required once when you first create the virtual environment.
> If you have multiple versions of Python 3+ installed, use version 3.6 or greater for the following command (i.e. python3.7 instead of python3).

```sh
python3 -m venv venv
```

- To start the virtual environment, run:

```sh
. venv/bin/activate
```

- A `(venv)` will be displayed at the front of your terminal prompt. This lets you know that you're using this virtual environment.

- Now update Python tools and install the libraries used by the various Python tools:

```sh
pip install --upgrade setuptools pip
pip install pandas==0.23.4
pip install sqlalchemy==1.2.18
pip install pyhive[presto]
```

- It is possible to exit the *venv* at any time with the command below:

```sh
deactivate
```
