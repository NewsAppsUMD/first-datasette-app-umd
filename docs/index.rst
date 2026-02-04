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

.. admonition:: 🎯 Deep Dive Challenge
   :class: warning

   **Challenge 1: Understanding Your History**

   Run ``git log --oneline`` to see your commit history. Now imagine you're working on a months-long
   data journalism project with a team.

   - How would you structure your commits differently for a complex project?
   - What would happen if you needed to undo a change from two weeks ago?
   - Research: What is ``git diff`` and how might it help you review changes before committing?

   **Challenge 2: Branching Thinking**

   Ask your AI assistant to explain Git branches. Then consider:

   - Why might you create a branch called ``experimental-analysis`` before trying a risky data transformation?
   - How do branches enable collaboration without conflicts?
   - What's the relationship between branches and pull requests on GitHub?

.. admonition:: 📝 Substantive Reflection Assignment
   :class: important

   **Written Reflection** (Discuss thoroughly with your AI assistant):

   1. **Workflow Design**: Design a Git workflow for a data journalism project that involves:
      - Raw data that should never be modified
      - Cleaned/processed data that evolves over time
      - Analysis scripts that change frequently
      - Final published outputs

      How would you organize commits? What would your commit message conventions be?

   2. **Failure Scenarios**: What could go wrong without version control? Describe a realistic
      scenario where a journalist loses work or publishes incorrect data because they didn't
      use version control. How would Git have prevented this?

   3. **Beyond Code**: Git isn't just for code. What other artifacts in a newsroom might benefit
      from version control? (Think: data dictionaries, methodology documents, fact-check records)

   When you can articulate these concepts clearly, move on to Act 2!

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

.. admonition:: 🎯 Deep Dive Challenge
   :class: warning

   **Challenge 1: Data Inspection**

   Before moving on, explore your data more deeply:

   .. code-block:: bash

       $ sqlite-utils schema maryland_grants.db

   This shows you the database structure. Now:

   - What columns exist in the grants table? Are the data types appropriate?
   - Run ``sqlite-utils rows maryland_grants.db grants --limit 5`` to see sample data
   - Identify at least THREE potential data quality issues (missing values, inconsistent formatting, wrong types)

   **Challenge 2: Command Mastery**

   Explore sqlite-utils capabilities by running ``sqlite-utils --help``. Find and explain:

   - How would you add a second CSV file to create a new table in the same database?
   - What does ``sqlite-utils tables maryland_grants.db --counts`` tell you?
   - How could you extract specific columns into a new table?

   **Challenge 3: Data Journalism Thinking**

   Look at the actual data in your grants.csv file. Consider:

   - What questions could a journalist ask of this dataset?
   - What's missing that would make the story more complete? (Think: inflation adjustment, recipient details, geographic data)
   - If you were to FOIA additional data to supplement this, what would you request?

.. admonition:: 📝 Substantive Reflection Assignment
   :class: important

   **Written Reflection** (Discuss thoroughly with your AI assistant):

   1. **Database Design Critique**: The current setup has one table. Is this optimal?
      Design an improved database schema that might include:
      - A separate table for grant recipients
      - A table for fiscal year metadata
      - A lookup table for grant categories

      Explain the concept of "normalization" and why it matters for data integrity.

   2. **Automation Planning**: Imagine this grant data is updated monthly by the state.
      How would you design an automated pipeline that:
      - Downloads new data
      - Validates it for quality issues
      - Loads it into your database
      - Alerts you to anomalies

      What tools beyond sqlite-utils might you need?

   3. **Comparative Analysis**: Research ONE alternative to sqlite-utils (such as ``csvkit``,
      ``pandas``, or ``duckdb``). What are its strengths compared to sqlite-utils?
      When would you choose one over the other?

   When you can discuss database design principles confidently, move on to Act 3!

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

.. admonition:: 🎯 Deep Dive Challenge
   :class: warning

   **Challenge 1: Plugin Ecosystem Exploration**

   Datasette's power comes from its plugin ecosystem. Research these questions:

   - Visit https://datasette.io/plugins and find THREE plugins that would be useful for journalism
   - What does ``datasette-export-notebook`` do? How might this help a data journalist?
   - What does ``datasette-cluster-map`` do? What kind of data would you need to use it?
   - Install one additional plugin and demonstrate its functionality

   **Challenge 2: Metadata Configuration**

   Create a ``metadata.yaml`` file to customize your Datasette instance:

   .. code-block:: yaml

       title: Maryland Grant and Loan Database
       description: FY2009-FY2022 grant and loan data from Maryland's Open Data Portal
       license: Public Domain
       license_url: https://opendata.maryland.gov/
       databases:
         maryland_grants:
           tables:
             grants:
               description: Individual grant and loan records

   Then run: ``datasette serve maryland_grants.db --metadata metadata.yaml``

   Now explore: What other metadata options are available? How would you add:
   - Column descriptions
   - Default sort orders
   - Hidden columns (for sensitive data)

   **Challenge 3: Understanding the Architecture**

   Using your browser's developer tools (F12), examine the network requests when you:
   - Load the main page
   - Sort by a column
   - Apply a filter

   What format does Datasette use for its API responses? How could a programmer use this API
   to build applications on top of your data?

.. admonition:: 📝 Substantive Reflection Assignment
   :class: important

   **Written Reflection** (Discuss thoroughly with your AI assistant):

   1. **Datasette vs. Alternatives**: Research and compare Datasette to:
      - A traditional database viewer (like DB Browser for SQLite)
      - A spreadsheet application (like Google Sheets)
      - A BI tool (like Tableau Public or Metabase)

      Create a comparison matrix: When would you use each tool? What are Datasette's unique strengths?

   2. **Publication Strategy**: You're a data editor at a newsroom. Design a Datasette deployment strategy:
      - What data should be public vs. internal-only?
      - How would you handle sensitive columns (names, addresses)?
      - What authentication options does Datasette offer?
      - How would you document your data for external users?

   3. **API-First Thinking**: Datasette automatically creates a JSON API. Design a small web application
      (describe it conceptually) that would consume your grants API to:
      - Show a searchable directory of grant recipients
      - Visualize grant trends over time
      - Alert users when new large grants are added

      What API endpoints would you use? What would the user experience be?

   4. **Plugin Development Ideation**: If you could create a Datasette plugin for journalism, what would it do?
      Describe the functionality, the problem it solves, and how journalists would use it.

   When you understand Datasette's role in the data publishing ecosystem, move on to Act 4!

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

.. admonition:: 🎯 Deep Dive Challenge
   :class: warning

   **Challenge 1: Complete Data Type Audit**

   The Amount column was just the beginning. Perform a complete audit:

   .. code-block:: bash

       $ sqlite-utils schema maryland_grants.db

   For EACH column, determine:
   - What data type is it currently?
   - What data type SHOULD it be?
   - What problems might the wrong type cause?

   Create a transformation plan and execute it. Hint: Dates stored as text are a common problem.

   **Challenge 2: Data Validation**

   Now that types are correct, check for data quality issues:

   .. code-block:: sql

       SELECT * FROM grants WHERE Amount < 0;
       SELECT * FROM grants WHERE Amount IS NULL;
       SELECT DISTINCT "Fiscal Year" FROM grants ORDER BY "Fiscal Year";

   (Run these in Datasette's SQL interface)

   - Are there negative amounts? What might they represent?
   - Are there missing values? How should they be handled?
   - Are fiscal years formatted consistently?

   **Challenge 3: Computed Columns**

   sqlite-utils can add computed columns. Research how you might:
   - Add an ``amount_millions`` column that divides Amount by 1,000,000
   - Add a ``decade`` column that groups years into decades
   - Add a ``grant_size_category`` column (small/medium/large based on amount thresholds)

   Implement at least ONE computed column and explain your methodology.

   **Challenge 4: Transformation Scripting**

   Write a shell script (or describe the steps) that would:
   1. Download fresh data from the source
   2. Apply all necessary type transformations
   3. Add computed columns
   4. Validate the data
   5. Start Datasette with appropriate metadata

   How would you make this reproducible for others?

.. admonition:: 📝 Substantive Reflection Assignment
   :class: important

   **Written Reflection** (Discuss thoroughly with your AI assistant):

   1. **Data Provenance**: You've now modified the original data (type conversions, computed columns).
      How do you document what you've done? Design a "data diary" or changelog that would help:
      - Future you understand what transformations were applied
      - An editor verify your methodology
      - A reader assess the reliability of your analysis

   2. **Defensive Data Practices**: What could go wrong when transforming data?
      - What if a type conversion fails silently (e.g., "N/A" becomes 0)?
      - How would you detect if the source data format changed?
      - Design validation checks that should run after every transformation

   3. **Inflation and Normalization**: The grants span FY2009-FY2022. A $1M grant in 2009 is not
      equivalent to $1M in 2022 due to inflation.
      - Research how you would add inflation-adjusted amounts
      - What data source would you use for inflation rates?
      - Why does this matter for comparing grants across years?

   4. **Schema Evolution**: Imagine the state adds new columns next year (e.g., "Congressional District").
      How would you handle:
      - Adding the new column to your database
      - Historical records that don't have this data
      - Maintaining backward compatibility with existing analyses

   When you understand data transformation principles and their implications, move on to Act 5!

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

.. admonition:: 🔍 Guided Exploration Activities
   :class: tip

   **Phase 1: Feature Mastery**

   Work with your AI assistant to master each Datasette feature:

   1. **Facets Deep Dive**:
      - Apply facets to at least 3 different columns simultaneously
      - What's the difference between a facet and a filter?
      - When would you use facet_size to show more/fewer options?

   2. **Advanced Filtering**:
      - Create a compound filter (e.g., grants > $1M AND Fiscal Year = 2020)
      - Use the "contains" filter on text columns
      - Export your filtered results as CSV and JSON

   3. **SQL Fundamentals** (run these in the SQL interface):

      .. code-block:: sql

          -- Total grants by fiscal year
          SELECT "Fiscal Year", COUNT(*) as grant_count, SUM(Amount) as total_amount
          FROM grants
          GROUP BY "Fiscal Year"
          ORDER BY "Fiscal Year";

          -- Top 10 grant recipients by total amount
          SELECT Grantee, COUNT(*) as grants, SUM(Amount) as total
          FROM grants
          GROUP BY Grantee
          ORDER BY total DESC
          LIMIT 10;

      Modify these queries to answer YOUR questions about the data.

.. admonition:: 🎯 Deep Dive Challenge: Investigative Data Journalism
   :class: warning

   **Challenge 1: Find a Story**

   Every dataset has stories. Your task is to find THREE potential news stories in this data.
   For each story:
   - Write a one-sentence news hook
   - Identify the SQL query or filters that reveal the story
   - List what additional reporting would be needed to verify and complete the story

   Example approaches:
   - Look for outliers (unusually large/small grants)
   - Look for trends over time (increasing/decreasing funding)
   - Look for concentration (does one recipient dominate?)
   - Look for absence (who ISN'T getting grants?)

   **Challenge 2: Advanced SQL Analysis**

   Write SQL queries to answer these journalism questions:

   .. code-block:: sql

       -- Year-over-year change in total grant funding
       -- (Hint: Use LAG() window function or self-join)

       -- Identify grantees who received funding every single year

       -- Find the grant "category" with the highest average award size

       -- Identify potential duplicate records

   Ask your AI assistant for help with complex SQL syntax, but try to design the logic yourself first.

   **Challenge 3: Data Joining Concept**

   This dataset alone tells part of a story. Research what other datasets you could JOIN to enrich it:

   - Census data (to normalize grants per capita by region)
   - Political contribution data (to investigate relationships)
   - Organizational data (to understand recipient types)
   - Economic indicators (to contextualize grant amounts)

   For ONE of these potential joins:
   - Find a real dataset online that could be joined
   - Identify the "join key" that would connect the datasets
   - Describe what new questions you could answer with the combined data

   **Challenge 4: Replication Package**

   Create a complete "replication package" that would allow another journalist to:
   - Reproduce your exact database from raw data
   - Understand all transformations applied
   - Run your key analytical queries
   - Verify your findings

   What files would this package include? Write the README for this package.

.. admonition:: 🚀 Extension Activities
   :class: tip

   **For Advanced Students** - Choose at least ONE:

   1. **Deploy to the Cloud**: Research how to deploy Datasette to:
      - Vercel (free tier available)
      - Google Cloud Run
      - Fly.io

      What are the tradeoffs between hosting options?

   2. **Custom Visualization**: Use ``datasette-vega`` plugin to create a visualization:
      - Install the plugin
      - Create a chart showing grant trends over time
      - Embed the chart in a custom page

   3. **Datasette Write**: Research ``datasette-write`` and consider:
      - When would you want users to be able to write to your database?
      - What security considerations apply?
      - Design a use case where writable Datasette makes sense

   4. **GraphQL Interface**: Install ``datasette-graphql`` and:
      - Write a GraphQL query to fetch grant data
      - Compare GraphQL to REST API - when would you use each?

   5. **Full-Text Search**: Install ``datasette-sqlite-fts`` and:
      - Enable full-text search on the grants table
      - Test search functionality
      - When is full-text search better than SQL LIKE queries?

When we're done, you can add your changes to your GitHub repository and push them:

.. code-block:: bash

    $ git add .
    $ git commit -m "Completed Datasette tutorial with enhanced analysis"
    $ git push origin main

.. admonition:: 🎉 Final Capstone Assignment
   :class: important

   **Capstone Project Proposal**

   Before completing this tutorial, prepare a proposal for a complete data journalism project
   using the skills you've learned. Discuss with your AI assistant and document:

   **Part 1: Dataset Selection**
   - Identify a real dataset you want to analyze (government data, public records, etc.)
   - Why is this dataset newsworthy?
   - What's the source and how reliable is it?
   - What are the ethical considerations?

   **Part 2: Technical Plan**
   - What data cleaning/transformation will be needed?
   - What computed columns would be useful?
   - What Datasette plugins would enhance the project?
   - How will you deploy/share the final product?

   **Part 3: Analytical Approach**
   - What are your three main research questions?
   - What SQL queries will help answer them?
   - What external data might you join?
   - What are potential pitfalls in interpretation?

   **Part 4: Publication Strategy**
   - Who is your audience?
   - How will you make the data accessible to non-technical readers?
   - What documentation will you provide?
   - How will you handle corrections/updates?

.. admonition:: 📝 Comprehensive Reflection
   :class: important

   **Technical Mastery Checklist** (Verify you can explain each):

   □ Git: staging, committing, pushing, branches, pull requests
   □ SQLite: tables, data types, queries, joins
   □ sqlite-utils: insert, transform, schema, computed columns
   □ Datasette: serve, plugins, metadata, facets, filters, SQL interface
   □ Data types: text vs. numeric, dates, handling nulls
   □ Data journalism: finding stories, verification, documentation

   **Critical Thinking Questions**:

   1. **Limitations**: What CAN'T Datasette do? When would you need different tools?

   2. **Ethics**: What are the ethical implications of making government data more accessible?
      - Could bad actors misuse this data?
      - What responsibility do you have to contextualize data?
      - When should data NOT be published?

   3. **Sustainability**: How do you maintain a data project over time?
      - What happens when source data changes?
      - How do you handle corrections?
      - What's your archival strategy?

   4. **Collaboration**: How would you use these tools in a newsroom team?
      - How do you share access?
      - How do you coordinate analysis?
      - How do you ensure reproducibility?

   **Next Learning Pathways**:

   Based on what you've learned, discuss with your AI assistant which pathway interests you:

   - **Data Engineering**: Learn about data pipelines, ETL, Apache Airflow
   - **Data Visualization**: Learn D3.js, Observable, Datawrapper
   - **Investigative Methods**: Learn OSINT, document analysis, records requests
   - **Programming**: Deepen Python/SQL skills, learn pandas, jupyter
   - **Deployment**: Learn Docker, cloud platforms, CI/CD

   **Share your work**: Your Datasette app is now running! Consider:
   - Sharing the URL with classmates
   - Exploring the example Datasette instances at https://datasette.io/examples
   - Reading the Datasette documentation: https://docs.datasette.io/
   - Joining the Datasette community: https://datasette.io/discord

   Thank you for working through this tutorial. The skills you've learned are the foundation
   of modern data journalism. Keep exploring, keep questioning, and keep publishing!
