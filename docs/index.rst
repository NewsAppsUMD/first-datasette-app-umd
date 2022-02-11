:tocdepth: 2

==============
First Datasette App
==============

A step-by-step guide to publishing a simple Datasette application.

This tutorial will walk you through the process of building an interactive Datasette application
from a structured dataset. You will get hands-on experience in every stage of the
development process  while recording it in Git's version control system. By the end you will have
published your work on the World Wide Web.

******************
What you will make
******************

By the end of this lesson, you will publish an interactive database about House of Representatives
office expenditures. You will do this by using the data published by `ProPublica <https://projects.propublica.org/represent/expenditures>`_.

A working example of what you'll make can be found at `https://house-expenses-umd.herokuapp.com/ <https://house-expenses-umd.herokuapp.com/>`_

.. image:: /_static/datasette.png

You can see `examples of Datasette in action <https://datasette.io/examples>_`, many of which are journalistic in nature or adjacent.

*****************
About the authors
*****************

This guide was prepared for a class in News Applications Development at the Philip Merrill College of
Journalism at the University of Maryland by Derek Willis.

**********************
Prelude: Prerequisites
**********************

Before you can begin, your computer needs the following tools installed and working.

1. A `command-line interface <https://en.wikipedia.org/wiki/Command-line_interface>`_ to interact with your computer
2. A `text editor <https://en.wikipedia.org/wiki/Text_editor>`_ to work with plain text files
3. Version 3.X of the `Python <https://www.python.org/downloads/>`_ programming language
4. The `pipenv <https://pipenv.pypa.io/en/latest/>`_ package and virtual environment manager for Python
5. `Git <https://git-scm.com/>`_ version control software and an account at `GitHub.com <http://www.github.com>`_
6. The `wget <https://www.jcchouinard.com/wget/>`_ command-line utility
7. A `Heroku <https://www.heroku.com/>`_ account for deploying the app, including the Heroku command-line tools

.. warning::

    Stop and make sure you have all these tools installed and working properly. Otherwise, `you're gonna have a bad time <https://www.youtube.com/watch?v=ynxPshq8ERo>`_.

.. _command-line-prereq:

Command-line interface
~~~~~~~~~~~~~~~~~~~~~~

Unless something is wrong with your computer, there should be a way to open a window that lets you type in commands. Different operating systems give this tool slightly different names, but they all have some form of it.

On Windows you can find the command-line interface by opening the "command prompt." Here are `instructions <https://www.bleepingcomputer.com/tutorials/windows-command-prompt-introduction/>`_.

On Apple computers, you open the `"Terminal" application <https://blog.teamtreehouse.com/introduction-to-the-mac-os-x-command-line>`_.

Ubuntu Linux comes with a program of the `same name <https://askubuntu.com/questions/38162/what-is-a-terminal-and-how-do-i-open-and-use-it>`_.

Text editor
-----------

A program like Microsoft Word, which can do all sorts of text formatting like
change the size and color of words, is not what you need. Do not try to use it.

You need a program that works with simple `"plain text" files <https://en.wikipedia.org/wiki/Text_file>`_,
and is therefore capable of editing documents containing Python code, HTML markup and other languages without
dressing them up by adding anything extra. Such programs are easy to find and some of the best ones are free, including those below.

For Windows, I recommend installing `Notepad++ <https://notepad-plus-plus.org/>`_.

For Apple computers, try `Sublime Text <https://www.sublimetext.com/>`_ or `Atom <https://atom.io/>`_.

In Ubuntu Linux you can stick with the pre-installed `gedit <https://help.ubuntu.com/community/gedit>`_ text editor.

Python
~~~~~~

Python is a computer programming language, like many others you may have heard of such as Ruby or PHP or Java. It is free and open source. We'll be installing Python 3 in a virtual environment, so it doesn't matter what version you have installed currently.

For Mac
^^^^^^^

If you are using Mac OSX, Python version 2.7 is probably already installed, but we'll be using Python 3. To install that, we'll be using `Homebrew <https://docs.python-guide.org/starting/install3/osx/#install3-osx>`_.

To install Python via Homebrew, you can run the following code:

.. code-block:: bash

    $ brew install python

.. note::

    You'll note that the example above begins with a "$". You do not need to type this. It is only a generic symbol
    commonly used by geeks to indicate a piece of code should be run from the command line. On Windows, this prompt could even look quite different, likely starting with a phrase like ``C:\``.

You should see something like this after you hit enter:

.. code-block:: bash

    $ python -V
    Python 3.9.7


For Windows
^^^^^^^^^^^

Windows people should follow the instructions `here <https://docs.python-guide.org/starting/install3/win/#install3-windows>`_.

.. _command-line-pipenv:

pipenv
~~~~~~~~~~~~~~~~~~

The `pipenv package manager <https://pipenv.pypa.io/>`_ makes it easy to install open-source libraries that expand what you're able to do with Python. Later, we will use it to install everything needed to create a working web application.

Verify pipenv is installed with the following command:

.. code-block:: bash

    $ pipenv -v

If you get and error, that means you don't have pipenv installed. You can get it by following `these instructions <https://pipenv.pypa.io/en/latest/install/#pragmatic-installation-of-pipenv>`_.

Git and GitHub
--------------

`Git <http://git-scm.com/>`_ is a version control program for saving the changes you make to files over time. This is useful when you're working on your own, but quickly becomes essential with large software projects when you work with other developers.

`GitHub <https://github.com/>`_ is a website that hosts git code repositories, both public and private. It comes with many helpful tools for reviewing code and managing projects. It also has some `extra tricks <http://pages.github.com/>`_ that make it easy to publish web pages, which we will use later.

GitHub offers helpful guides for installing Git for `Windows <https://help.github.com/articles/set-up-git#platform-windows>`_, `Macs <https://help.github.com/articles/set-up-git#platform-mac>`_ and `Linux <https://help.github.com/articles/set-up-git#platform-linux>`_.

You can verify it's installed from your command line like so:

.. code-block:: bash

    $ git --version

Once that's done, you should create an account at GitHub, if you don't already have one. `The free plan <https://github.com/pricing>`_ is all that's required to complete this lesson.

.. _activate:

****************
Act 1: Hello Git
****************

Start at our first-news-app directory.

.. code-block:: bash

    $ cd first-datasette-app

Create a new development environment with pipenv, specifying the version of python:

.. code-block:: bash

    # You don't have to type the "$" It's just a generic symbol
    # geeks use to show they're working on the command line.
    $ pipenv --python=python3

Then activate it (it's like turning on the power):

.. code-block:: bash

    $ pipenv shell

Create a new Git repository.

.. code-block:: bash

    $ git init repo

Visit `GitHub <http://www.github.com>`_ and create a new public repository named ``first-datasette-app``. Don't check "Initialize with README." You want to start with a blank repository.

Then connect your local directory to GitHub with the following command.

.. code-block:: bash

    $ git remote add origin https://github.com/<yourusername>/first-datasette-app.git

Create your first file, a blank ``README`` with a `Markdown <https://en.wikipedia.org/wiki/Markdown>`_ file extension since that's `the preferred format of GitHub <https://help.github.com/articles/github-flavored-markdown>`_.

.. code-block:: bash

    # Macs or Linux:
    $ touch README.md
    # In Windows fire it up in your text editor right away:
    $ start notepad++ README.md

Open up the README in your text editor and type something in it. Maybe something like:

.. code-block:: markdown

    My first Datasette app
    =================

Make sure to save it. Then officially add the file to your repository for tracking with Git's ``add`` command.

.. code-block:: bash

    $ git add README.md

Log its creation with Git's ``commit`` command. You can include a personalized message after the ``-m`` flag.

.. code-block:: bash

    $ git commit -m "First commit"

If this is your first time using Git, you may be prompted to configure you name and email.
If so, take the time now. Then run the ``commit`` command above again.

.. code-block:: bash

    $ git config --global user.email "your@email.com"
    $ git config --global user.name "your name"

Now, finally, push your commit up to GitHub.

.. code-block:: bash

    $ git push origin main

Reload your repository on GitHub and see your handiwork.

******************
Act 2: Hello sqlite-utils
******************

Use pipenv on the command line to install `sqlite-utils <https://sqlite-utils.datasette.io/en/stable/>`_, the Python library we'll use to load our data.

.. code-block:: bash

    $ pipenv install sqlite-utils

You can check to see if the library installed using the command-line:

.. code-block:: bash

    $ sqlite-utils

Let's grab two CSV files and load them into a SQLite database we'll create using the sqlite-utils library.

Create a directory for your data files and change into it.

.. code-block:: bash

    $ mkdir data
    $ cd data

Use wget on the command line to download the CSV files, renaming them using the -O switch:

.. code-block:: bash

    $ wget https://projects.propublica.org/congress/assets/staffers/2021Q2-house-disburse-summary.csv -O summary.csv
    $ wget https://projects.propublica.org/congress/assets/staffers/2021Q2-house-disburse-detail.csv -O detail.csv

Use sqlite-utils on the command line to load the files into a SQLite database that we'll call house_expenses.db:

.. code-block:: bash

    $ cd .. # move up to the main directory
    $ sqlite-utils insert house_expenses.db summary data/summary.csv --csv
    $ sqlite-utils insert house_expenses.db detail data/detail.csv --csv

*****************
Act 3: Hello Datasette
*****************

Use pipenv on the command line to install `Datasette <https://datasette.io/>`_, the Python library we'll use to publish our data.

.. code-block:: bash

    $ pipenv install datasette

You can check to see if the library installed using the command-line:

.. code-block:: bash

    $ datasette

Now let's fire up Datasette's built-in server to run the app locally:

.. code-block:: bash

    $ datasette serve house_expenses.db

Switch to your browser and navigate to `http://127.0.0.1:8001/ <http://127.0.0.1:8001/>`_ to see your running app.

*********************
Act 4: Customizing Datasette
*********************

Let's look at the `summary <http://127.0.0.1:8001/house_expenses/summary>`_ table - and click on the AMOUNT header,
which should sort the amounts. You can see that SQLite doesn't seem to think the values in this columm are numbers;
instead it is sorting them as text. Let's fix that.

On the command-line, hit Ctrl-C to stop the local server and change some of the columns in our house_expenses.db file
to the correct datatypes:

.. code-block:: bash

    $ sqlite-utils transform house_expenses.db summary --type AMOUNT float --type YTD float --type YEAR integer --type QUARTER integer
    $ sqlite-utils transform house_expenses.db detail --type AMOUNT float --type YEAR integer --type QUARTER integer

Now let's try that server again:

.. code-block:: bash

    $ datasette serve house_expenses.db

Now you can see that if you sort AMOUNT in descending order the `results <http://127.0.0.1:8001/house_expenses/summary?_sort_desc=AMOUNT>`_
are arranged correctly.

*********************
Act 5: Hello Internet
*********************

In this final act, we will publish your application to the Internet on Heroku. To do this you will need to have a Heroku account
and install the `Heroku command-line tool <https://devcenter.heroku.com/articles/heroku-cli>`_. Once you've done that, you can
use the `datasette publish heroku` command to publish your app. Replace `firstmiddlelast` with your initials:

.. code-block:: bash

    $ datasette publish heroku house_expenses.db -n house-expenses-firstmiddlelast

Now wait a minute or two, then visit ``https://house-expenses-firstmiddlelast.herokuapp.com/`` to see your Datasette application.

For Windows 10 users, this step likely will not work - at least, not yet. There is a workaround for publishing on Heroku, but it involves editing local Datasette files. Here's the process:

1. Find the Datasette folder on your system. Depending on where you installed it, it may be in a hidden virtual environment folder. (Use the file explorer. It may need to crank for a while. If you know where it's probably located, check "view hidden items" in file explorer and poke around.)
2. In the datasette/publish directory, open heroku.py for editing in a text editor. You will need to add the phrase `shell=True` in five locations:

.. code-block:: python

    plugins = [
        line.split()[0] for line in check_output(["heroku", "plugins"], shell=True).splitlines()
    ]

    if name:
        # Check to see if this app already exists
        list_output = check_output(["heroku", "apps:list", "--json"], shell=True).decode(
            "utf8"
        )

    create_output = check_output(cmd, shell=True).decode("utf8")

    call(["heroku", "config:set", "-a", app_name, f"{key}={value}"], shell=True)

    call(
        ["heroku", "builds:create", "-a", app_name, "--include-vcs-ignore"]
        + tar_option, shell=True
    )

3. Save heroku.py and run the previous datasette publish command in your shell. If you get an "install heroku builds?" message, hit "y" -- then observe that it maybe didn't work.
4. Enter heroku plugins:install heroku-builds manually; you should see install activity.
5. Try the publish command again. Watch the recursive error unfold. If you scroll back up, you should see that your site published even though you get the recursion error.
