:tocdepth: 2

==============
First Datasette App
==============

A step-by-step guide to publishing a simple Datasette application.

This tutorial will walk you through the process of building an interactive Datasette application
from a structured dataset. You will get hands-on experience in every stage of the
development process while recording it in Git's version control system. By the end you will have
published your work on the World Wide Web.

.. note::
   **Interactive Learning Mode**: This tutorial is designed to be completed with an AI assistant (Claude Code or GitHub Copilot).
   The assistant will guide you through the material using a Socratic approach, asking questions to deepen your understanding.
   Don't hesitate to ask questions, experiment, and explore beyond the basic instructions!

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

.. admonition:: 🤔 Interactive Checkpoint
   :class: tip

   **Before you begin Act 1**: Chat with your AI assistant about what you hope to learn from this tutorial.
   Ask them to explain what we'll accomplish in this first act before diving in.

Start at the `GitHub URL for this repository <https://github.com/NewsAppsUMD/first-datasette-app-umd>`_

Click the green "Use this template" button and choose "Open in a codespace". You should see something like this:

.. image:: /_static/codespaces.png

The browser is divided into three sections: on the left is a file explorer, listing all of the files in this repository. The top right shows whatever file you're currently viewing or editing, defaulting to README.md. The bottom right shows the terminal, where we'll run commands.

The codespace will be connected to your repository in the `the NewsApps organization on GitHub <https://github.com/NewsAppsUMD/>`_.

.. admonition:: 💭 Pause and Discuss
   :class: tip

   **Before editing**: Ask your AI assistant to explain the three sections of the Codespaces interface.
   What do you think each section is used for? Describe what you see before reading further.

Open up the README by clicking on README.md on the left side and type something in it. Maybe change the heading like:

.. code-block:: markdown

    # My First Datasette App

Make sure to save it. You'll see on the left that there's a yellow "M" next to README.md, meaning you've made some edits. Let's double-check that in the terminal:

.. admonition:: 🤔 Think First
   :class: tip

   **Before running git status**: Ask your AI assistant what you expect to see when you run ``git status``.
   What does the "M" indicator mean? Why might we want to check the status?

.. code-block:: bash

    $ git status

You should see something like this:

.. image:: /_static/git_status.png

If so, we can add and commit it:

.. code-block:: bash

    $ git add README.md

Log its creation with Git's ``commit`` command. You can include a personalized message after the ``-m`` flag.

.. admonition:: 💬 Commit Message Practice
   :class: tip

   **Before committing**: Ask your AI assistant what makes a good commit message. Come up with your own
   commit message that describes what you changed. Discuss why clear commit messages matter.

.. code-block:: bash

    $ git commit -m "First commit"

Now, finally, push your commit up to GitHub.

.. code-block:: bash

    $ git push origin main

Reload your repository on GitHub and see your handiwork.

.. admonition:: ✅ Act 1 Complete - Reflection
   :class: important

   **Take a moment to reflect**: Discuss with your AI assistant:

   - What is Git and why do developers use version control?
   - Can you explain the three-step process: add, commit, push?
   - What did you find challenging or surprising about this act?

   When you feel confident, move on to Act 2!

******************
Act 2: Hello sqlite-utils
******************

.. admonition:: 🤔 Interactive Checkpoint
   :class: tip

   **Before you begin Act 2**: Discuss with your AI assistant:

   - What do you think "sqlite-utils" does based on its name?
   - Have you worked with databases before? What do you know about SQLite?
   - Why might we want to put CSV data into a database?

Use pip on the command line to install `sqlite-utils <https://sqlite-utils.datasette.io/en/stable/>`_, the Python library we'll use to load our data.

.. code-block:: bash

    $ pip install sqlite-utils

You can check to see if the library installed using the command-line:

.. code-block:: bash

    $ sqlite-utils

Let's grab our CSV file and load it into a SQLite database we'll create using the sqlite-utils library.

.. admonition:: 💭 Design Decision
   :class: tip

   **Before creating the data directory**: Ask your AI assistant why we might want to create a separate
   ``data`` directory instead of putting files in the root. What are some benefits of organizing
   projects into directories?

Create a directory for your data and change into it.

.. code-block:: bash

    $ mkdir data
    $ cd data

Use wget on the command line to download the CSV file, renaming them using the -O switch:

.. code-block:: bash

    $ wget https://raw.githubusercontent.com/NewsAppsUMD/data_files/refs/heads/main/State_of_Maryland_Grant_and_Loan_Data__FY2009_to_FY2022_20250131.csv -O grants.csv

Use sqlite-utils on the command line to load the files into a SQLite database that we'll call maryland_grants.db:

.. admonition:: 🤔 Understanding Commands
   :class: tip

   **Before running the command**: Ask your AI assistant to explain what each part of the
   ``sqlite-utils insert`` command does. What does the ``--csv`` flag mean?

.. code-block:: bash

    $ cd .. # move up to the main directory
    $ sqlite-utils insert maryland_grants.db grants data/grants.csv --csv

.. admonition:: ✅ Act 2 Complete - Reflection
   :class: important

   **Take a moment to reflect**: Discuss with your AI assistant:

   - What is the difference between a CSV file and a SQLite database?
   - What advantages does a database offer for working with data?
   - Can you explain what happened when you ran the ``sqlite-utils insert`` command?

   When you feel confident, move on to Act 3!

*****************
Act 3: Hello Datasette
*****************

.. admonition:: 🤔 Interactive Checkpoint
   :class: tip

   **Before you begin Act 3**: Discuss with your AI assistant:

   - What do you think Datasette does based on what we've learned so far?
   - What does it mean to "publish" data? Who might want to do this?
   - Have you used any web applications that display data? What were they like?

Use pip on the command line to install `Datasette <https://datasette.io/>`_, the Python library we'll use to publish our data.

.. code-block:: bash

    $ pip install datasette

You can check to see if the library installed using the command-line:

.. code-block:: bash

    $ datasette

Now let's fire up Datasette's built-in server to run the app locally:

.. admonition:: 💭 Think First
   :class: tip

   **Before starting the server**: Ask your AI assistant what "localhost" means and what happens
   when we start a server. What do you expect to see?

.. code-block:: bash

    $ datasette serve maryland_grants.db

On the lower right, you should see a small window pop up with the message that you can "Open in Browser".

.. image:: /_static/open_in_browser.png

Click on that button to see your running app.

Oh, that doesn't work. We need to install a specific Datasette tool to work with codespaces.

.. admonition:: 🔍 Troubleshooting Moment
   :class: tip

   **When something doesn't work**: This is a learning opportunity! Ask your AI assistant:

   - What error did you see (if any)?
   - Why might the standard Datasette setup not work in Codespaces?
   - What do you think a "datasette-codespaces" plugin might do?

.. code-block:: bash

    $ datasette install datasette-codespaces

Now try running the server again:

.. code-block:: bash

    $ datasette serve maryland_grants.db

.. admonition:: ✅ Act 3 Complete - Reflection
   :class: important

   **Take a moment to reflect**: Discuss with your AI assistant:

   - What is Datasette and what problem does it solve?
   - Can you explain how the server-client relationship works?
   - What did you learn from troubleshooting the Codespaces issue?
   - Explore the Datasette interface - what features do you notice?

   When you feel confident, move on to Act 4!

*********************
Act 4: Customizing Datasette
*********************

.. admonition:: 🤔 Interactive Checkpoint
   :class: tip

   **Before you begin Act 4**: Discuss with your AI assistant:

   - Look at the Datasette interface. Try clicking on the "Amount" column header to sort.
   - What do you notice about how the amounts are sorted?
   - Why do you think this is happening?

Let's look at the summary table - and click on the Amount header, which should sort the amounts. You can see that SQLite doesn't seem to think the values in this columm are numbers;
instead it is sorting them as text. Let's fix that.

.. admonition:: 💭 Understanding Data Types
   :class: tip

   **Before fixing**: Ask your AI assistant:

   - What's the difference between storing "100" as text vs. as a number?
   - What is a "float" data type?
   - Why does the data type matter for sorting?

Back in the terminal, hit Ctrl-C to stop the local server and change some of the columns in our maryland_grants.db file
to the correct datatypes:

.. code-block:: bash

    $ sqlite-utils transform maryland_grants.db grants --type Amount float 

Now let's try that server again:

.. code-block:: bash

    $ datasette serve maryland_grants.db

Now you can see that if you sort Amount in descending order the results are arranged correctly.

.. admonition:: ✅ Act 4 Complete - Reflection
   :class: important

   **Take a moment to reflect**: Discuss with your AI assistant:

   - What changed when you transformed the Amount column to a float?
   - Can you think of other columns that might need type corrections?
   - Why is data typing important in databases and data analysis?

   When you feel confident, move on to Act 5!

*********************
Act 5: Exploring Data with Datasette
*********************

.. admonition:: 🤔 Interactive Checkpoint
   :class: tip

   **Before you begin Act 5**: This is your chance to explore! Discuss with your AI assistant:

   - What questions would you like to ask about Maryland grant data?
   - Look at the Datasette interface - what features haven't we explored yet?
   - What is a "facet" in data analysis?

In this final act, we will explore some of Datasette's features, including facets, filters and custom SQL queries.

.. admonition:: 🔍 Exploration Activities
   :class: tip

   **Interactive exploration**: Work with your AI assistant to try these activities:

   1. **Facets**: Click "Facet" on different columns. What patterns do you see?
   2. **Filters**: Create filters to find specific grants. What can you discover?
   3. **Sorting**: Find the largest and smallest grants. Who received them?
   4. **SQL**: Try writing a custom SQL query (ask your AI assistant for help!)

   Questions to explore:
   - Which organizations received the most grants?
   - What's the total amount of grants by year?
   - Are there any interesting patterns or outliers in the data?

When we're done, you can add your changes to your GitHub repository and push them:

.. code-block:: bash

    $ git add .
    $ git commit -m "finished tutorial!"
    $ git push origin main

.. admonition:: 🎉 Tutorial Complete - Final Reflection
   :class: important

   **Congratulations!** You've completed the First Datasette App tutorial. Take time to reflect with your AI assistant:

   **Technical Skills**:
   - Can you explain the full workflow from CSV to published database?
   - What role does each tool play: Git, sqlite-utils, Datasette?
   - What did you find most challenging? Most interesting?

   **Journalism Applications**:
   - How could you use Datasette for data journalism projects?
   - What kinds of datasets would be good candidates for this approach?
   - What stories could you tell with this Maryland grant data?

   **Next Steps**:
   - What would you like to explore next with Datasette?
   - How might you customize or extend this project?
   - What other datasets are you interested in analyzing?

   **Share your work**: Your Datasette app is now running! Consider:
   - Sharing the URL with classmates
   - Exploring the example Datasette instances at https://datasette.io/examples
   - Reading the Datasette documentation to learn about advanced features

   Thank you for working through this tutorial. Keep exploring and asking questions!
