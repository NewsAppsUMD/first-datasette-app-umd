:tocdepth: 2

==============
First Datasette App
==============

A step-by-step guide to publishing a simple Datasette application.

This tutorial will walk you through the process of building an interactive Datasette application
from a structured dataset. You will get hands-on experience in every stage of the
development process while recording it in Git's version control system. By the end you will have
published your work on the World Wide Web.

******************
What you will make
******************

By the end of this lesson, you will publish an interactive database about Maryland grant and loan data published 
by the state. You will do this by using the data published on `Maryland's Open Data Portal <https://opendata.maryland.gov/Budget/State-of-Maryland-Grant-and-Loan-Data-FY2009-to-FY/absk-avps/about_data>`_.

.. image:: /_static/datasette.png

You can see `examples of Datasette in action <https://datasette.io/examples>`_, many of which are journalistic in nature or adjacent.

*****************
About the authors
*****************

This guide was prepared for a class in News Applications Development at the Philip Merrill College of
Journalism at the University of Maryland by Derek Willis.

**********************
Prelude: Prerequisites
**********************

Before you can begin, your computer needs the following tools installed and working.

1. An account at `GitHub.com <http://www.github.com>`_

Seriously, that's it. Well, and a browser.

Git and GitHub
--------------

`GitHub <https://github.com/>`_ is a website that hosts git code repositories, both public and private. It comes with many helpful tools for reviewing code and managing projects. It also has some `extra tricks <http://pages.github.com/>`_ that make it easy to publish web pages, which we will use later.

You should create an account at GitHub, if you don't already have one. `The free plan <https://github.com/pricing>`_ is all that's required to complete this lesson.

.. _activate:

***********************
Act 1: Hello Codespaces
***********************

Start at the `GitHub URL for this repository <https://github.com/NewsAppsUMD/first-datasette-app-umd>`_

Click the green "Use this template" button and choose "Open in a codespace". You should see something like this:

.. image:: /_static/codespaces.png

The browser is divided into three sections: on the left is a file explorer, listing all of the files in this repository. The top right shows whatever file you're currently viewing or editing, defaulting to README.md. The bottom right shows the terminal, where we'll run commands.

The codespace will be connected to your repository in the `the NewsApps organization on GitHub <https://github.com/NewsAppsUMD/>`_.

Open up the README by clicking on README.md on the left side and type something in it. Maybe change the heading like:

.. code-block:: markdown

    # My First Datasette App

Make sure to save it. You'll see on the left that there's a yellow "M" next to README.md, meaning you've made some edits. Let's double-check that in the terminal:

.. code-block:: bash

    $ git status

You should see something like this:

.. image:: /_static/git_status.png

If so, we can add and commit it:

.. code-block:: bash

    $ git add README.md

Log its creation with Git's ``commit`` command. You can include a personalized message after the ``-m`` flag.

.. code-block:: bash

    $ git commit -m "First commit"

Now, finally, push your commit up to GitHub.

.. code-block:: bash

    $ git push origin main

Reload your repository on GitHub and see your handiwork.

******************
Act 2: Hello sqlite-utils
******************

Use pip on the command line to install `sqlite-utils <https://sqlite-utils.datasette.io/en/stable/>`_, the Python library we'll use to load our data.

.. code-block:: bash

    $ pip install sqlite-utils

You can check to see if the library installed using the command-line:

.. code-block:: bash

    $ sqlite-utils

Let's grab our CSV file and load it into a SQLite database we'll create using the sqlite-utils library.

Create a directory for your data and change into it.

.. code-block:: bash

    $ mkdir data
    $ cd data

Use wget on the command line to download the CSV file, renaming them using the -O switch:

.. code-block:: bash

    $ wget https://raw.githubusercontent.com/NewsAppsUMD/data_files/refs/heads/main/State_of_Maryland_Grant_and_Loan_Data__FY2009_to_FY2022_20250131.csv -O grants.csv

Use sqlite-utils on the command line to load the files into a SQLite database that we'll call maryland_grants.db:

.. code-block:: bash

    $ cd .. # move up to the main directory
    $ sqlite-utils insert maryland_grants.db grants data/grants.csv --csv

*****************
Act 3: Hello Datasette
*****************

Use pip on the command line to install `Datasette <https://datasette.io/>`_, the Python library we'll use to publish our data.

.. code-block:: bash

    $ pip install datasette

You can check to see if the library installed using the command-line:

.. code-block:: bash

    $ datasette

Now let's fire up Datasette's built-in server to run the app locally:

.. code-block:: bash

    $ datasette serve maryland_grants.db

On the lower right, you should see a small window pop up with the message that you can "Open in Browser".

.. image:: /_static/open_in_browser.png

Click on that button to see your running app.

Oh, that doesn't work. We need to install a specific Datasette tool to work with codespaces.

.. code-block:: bash

    $ datasette install datasette-codespaces

Now try running the server again:

.. code-block:: bash

    $ datasette serve maryland_grants.db

*********************
Act 4: Customizing Datasette
*********************

Let's look at the summary table - and click on the Amount header, which should sort the amounts. You can see that SQLite doesn't seem to think the values in this columm are numbers;
instead it is sorting them as text. Let's fix that.

Back in the terminal, hit Ctrl-C to stop the local server and change some of the columns in our maryland_grants.db file
to the correct datatypes:

.. code-block:: bash

    $ sqlite-utils transform maryland_grants.db grants --type Amount float 

Now let's try that server again:

.. code-block:: bash

    $ datasette serve maryland_grants.db

Now you can see that if you sort Amount in descending order the results are arranged correctly.

*********************
Act 5: Exploring Data with Datasette
*********************

In this final act, we will explore some of Datasette's features, including facets, filters and custom SQL queries.

When we're done, you can add your changes to your GitHub repository and push them:

.. code-block:: bash

    $ git add .
    $ git commit -m "finished tutorial!"
    $ git push origin main
