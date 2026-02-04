:tocdepth: 2

==============
First Datasette App
==============

A step-by-step guide to publishing a simple Datasette application.

This tutorial will walk you through the process of building an interactive Datasette application
from a structured dataset. You will get hands-on experience loading data, exploring it with
powerful tools, and publishing it for others to use. By the end you will have
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

GitHub
------

`GitHub <https://github.com/>`_ is a website that hosts code repositories, both public and private. It comes with many helpful tools for reviewing code and managing projects. It also has some `extra tricks <http://pages.github.com/>`_ that make it easy to publish web pages, which we will use later.

You should create an account at GitHub, if you don't already have one. `The free plan <https://github.com/pricing>`_ is all that's required to complete this lesson.

.. _activate:

**********************
Act 1: Hello sqlite-utils
**********************

.. admonition:: 🤔 Interactive Checkpoint
   :class: tip

   **Before you begin Act 1**: Discuss with your AI assistant:

   - What do you think "sqlite-utils" does based on its name?
   - Why might journalists want a fast way to work with data from the command line?
   - What kinds of data questions do you want to answer with Maryland grant data?

Start at the `GitHub URL for this repository <https://github.com/NewsAppsUMD/first-datasette-app-umd>`_

Click the green "Use this template" button and choose "Open in a codespace". You should see something like this:

.. image:: /_static/codespaces.png

The browser is divided into three sections: on the left is a file explorer, listing all of the files in this repository. The top right shows whatever file you're currently viewing or editing, defaulting to README.md. The bottom right shows the terminal, where we'll run commands.

The codespace will be connected to your repository in the `the NewsApps organization on GitHub <https://github.com/NewsAppsUMD/>`_.

Use pip on the command line to install `sqlite-utils <https://sqlite-utils.datasette.io/en/stable/>`_, the Python library we'll use to load and explore our data.

.. code-block:: bash

    $ pip install sqlite-utils

You can check to see if the library installed using the command-line:

.. code-block:: bash

    $ sqlite-utils --help

Let's grab our CSV file and load it into a SQLite database we'll create using the sqlite-utils library.

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

.. admonition:: 🎯 Deep Dive Challenge: sqlite-utils Power Features
   :class: warning

   **Challenge 1: Quick Data Profiling**

   sqlite-utils excels at rapid data exploration. Try these commands and discuss what you learn:

   .. code-block:: bash

       # How many records do we have?
       $ sqlite-utils rows maryland_grants.db grants --limit 5

       # What are the unique values in a column?
       $ sqlite-utils rows maryland_grants.db grants --columns "Fiscal Year" | sort | uniq -c

       # Quick counts
       $ sqlite-utils tables maryland_grants.db --counts

   **Challenge 2: Command-Line SQL**

   sqlite-utils lets you run SQL directly from the terminal - powerful for quick checks:

   .. code-block:: bash

       # Who got the biggest grants?
       $ sqlite-utils maryland_grants.db "SELECT Grantee, Amount FROM grants ORDER BY CAST(Amount AS REAL) DESC LIMIT 10"

       # How much total by year?
       $ sqlite-utils maryland_grants.db "SELECT \"Fiscal Year\", SUM(CAST(Amount AS REAL)) as total FROM grants GROUP BY \"Fiscal Year\""

   Practice writing your own queries. What questions about Maryland grants can you answer in one line?

   **Challenge 3: Data Extraction and Conversion**

   sqlite-utils can export data in multiple formats - useful for sharing with colleagues:

   .. code-block:: bash

       # Export query results as CSV
       $ sqlite-utils maryland_grants.db "SELECT * FROM grants WHERE CAST(Amount AS REAL) > 10000000" --csv > big_grants.csv

       # Export as JSON (for web developers or APIs)
       $ sqlite-utils maryland_grants.db "SELECT * FROM grants LIMIT 100" --json-cols

       # Export as newline-delimited JSON (for data pipelines)
       $ sqlite-utils rows maryland_grants.db grants --nl

   When would each format be most useful in a newsroom?

   **Challenge 4: Ongoing Data Maintenance**

   Real journalism projects require ongoing data updates. sqlite-utils makes this manageable:

   .. code-block:: bash

       # Append new data to existing table (e.g., when state releases new month's data)
       $ sqlite-utils insert maryland_grants.db grants new_grants.csv --csv

       # Insert with duplicate handling - skip records that already exist
       $ sqlite-utils insert maryland_grants.db grants new_grants.csv --csv --ignore

       # Or replace duplicates with newer versions (requires primary key)
       $ sqlite-utils insert maryland_grants.db grants new_grants.csv --csv --replace

       # Compare old vs new: find records in new file not in database
       $ sqlite-utils maryland_grants.db "SELECT * FROM grants WHERE rowid > (SELECT MAX(rowid) - 100 FROM grants)"

   Practice these maintenance scenarios:

   1. **Monthly updates**: The state releases new grant data each month. How do you:
      - Download and validate the new file
      - Check for unexpected changes in format or columns
      - Append only genuinely new records
      - Verify the update succeeded

   2. **Corrections**: The state issues corrections to previously reported data. How do you:
      - Identify which records need updating
      - Preserve a record of what changed (audit trail)
      - Update your database without losing history

   3. **Automation**: Design a simple update script that:
      - Downloads latest data from source
      - Compares to existing records
      - Alerts you to anomalies (sudden spike in grants, missing expected records)
      - Logs what was added/changed

.. admonition:: 📝 Substantive Reflection Assignment
   :class: important

   **Written Reflection** (Discuss thoroughly with your AI assistant):

   1. **Reporting Scenarios**: Describe THREE specific reporting scenarios where sqlite-utils
      command-line queries would be faster than opening Excel or Google Sheets:
      - A breaking news situation
      - A daily data check
      - A collaborative investigation

   2. **Data Maintenance Strategy**: You're setting up a long-term data project that will track
      Maryland grants over the next two years. Design your maintenance plan:
      - How often will you check for new data?
      - How will you detect if the source format changes?
      - How will you handle corrections to historical data?
      - What validation checks will you run after each update?
      - How will you document changes for your editor and readers?

      Write the actual sqlite-utils commands you would use for monthly updates.

   3. **Automation and Alerts**: Design an automated monitoring system:
      - What would trigger an alert? (New large grant? New recipient? Missing data?)
      - How would you notify yourself or your editor?
      - What would your "data health check" script look like?

   4. **Limitations**: What CAN'T sqlite-utils do well? When would you switch to:
      - A spreadsheet (Excel/Sheets)
      - A full programming environment (Python/pandas)
      - A visualization tool

   When you feel comfortable with data loading, querying, AND maintenance, move on to Act 2!

*****************
Act 2: Hello Datasette
*****************

.. admonition:: 🤔 Interactive Checkpoint
   :class: tip

   **Before you begin Act 2**: Discuss with your AI assistant:

   - What do you think Datasette does based on what we've learned so far?
   - Why might a journalist want to publish a database on the web?
   - Who is the audience for a published data tool vs. a published story?

Use pip on the command line to install `Datasette <https://datasette.io/>`_, the Python library we'll use to publish our data.

.. code-block:: bash

    $ pip install datasette

You can check to see if the library installed using the command-line:

.. code-block:: bash

    $ datasette --help

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

.. admonition:: 🎯 Deep Dive Challenge: Enrichments and Search
   :class: warning

   **Challenge 1: Full-Text Search**

   One of Datasette's most powerful features for journalists is full-text search.
   Let's enable it:

   .. code-block:: bash

       # Stop the server (Ctrl-C) and enable full-text search
       $ sqlite-utils enable-fts maryland_grants.db grants Grantee "Grant Category" Description --create-triggers

   Now restart Datasette and try searching for terms like "university" or "health".

   Explore:
   - How is full-text search different from SQL LIKE queries?
   - When would a reporter use search vs. filters?
   - What columns should be searchable for this dataset?

   **Challenge 2: Data Enrichment Plugins**

   Datasette's enrichment plugins can automatically enhance your data. Research these:

   - ``datasette-enrichments``: The base framework for enrichments
   - ``datasette-enrich-gpt``: Use AI to categorize or summarize text fields
   - ``datasette-geocode``: Add latitude/longitude to addresses
   - ``datasette-enrichments-re2``: Extract patterns using regex

   Install the enrichments framework:

   .. code-block:: bash

       $ datasette install datasette-enrichments

   Discuss with your AI assistant:
   - How could geocoding grant recipient addresses create a map story?
   - What text fields in our data could benefit from AI categorization?
   - What patterns might you extract with regex (dollar amounts, dates, IDs)?

   **Challenge 3: Search-Focused Plugins**

   Install and explore plugins that enhance discovery:

   .. code-block:: bash

       $ datasette install datasette-search-all

   This enables searching across ALL tables at once. Also research:

   - ``datasette-ripgrep``: Search within text columns using powerful regex
   - ``datasette-similar-records``: Find records that are similar to a selected one
   - ``datasette-saved-queries``: Save and share common searches

   How would each of these help a reporter on deadline?

   **Challenge 4: Datasette as an API**

   Every Datasette instance is automatically a full JSON API - no extra configuration needed.
   This is powerful for building applications, sharing data, and automation.

   Try these API endpoints in your browser (add ``.json`` to any URL):

   .. code-block:: text

       # Get table data as JSON
       /maryland_grants/grants.json

       # With filters
       /maryland_grants/grants.json?_where=Amount>1000000

       # With specific columns
       /maryland_grants/grants.json?_col=Grantee&_col=Amount

       # Paginated results
       /maryland_grants/grants.json?_size=100&_offset=200

       # SQL query results as JSON
       /maryland_grants.json?sql=SELECT+Grantee,+SUM(Amount)+as+total+FROM+grants+GROUP+BY+Grantee+ORDER+BY+total+DESC+LIMIT+10

   Explore the API:

   1. **From the command line** - Use curl to fetch data:

      .. code-block:: bash

          # Fetch top grantees as JSON
          $ curl "http://localhost:8001/maryland_grants/grants.json?_size=5" | python -m json.tool

   2. **Different formats** - Datasette supports multiple output formats:

      .. code-block:: text

          .json     - JSON (default)
          .csv      - CSV download
          .tsv      - Tab-separated values
          .nl       - Newline-delimited JSON (for streaming)

   3. **Build something** - Discuss with your AI assistant:
      - How could a newsroom website pull live data from this API?
      - How could you build a grant lookup widget for your news site?
      - How could another team's Python script consume this data?
      - What would a "grant alert" system look like that checks for new large grants?

   4. **CORS for external access** - For public APIs, enable cross-origin requests:

      .. code-block:: bash

          $ datasette serve maryland_grants.db --cors

      This allows JavaScript on other websites to fetch your data.

.. admonition:: 📝 Substantive Reflection Assignment
   :class: important

   **Written Reflection** (Discuss thoroughly with your AI assistant):

   1. **Search Strategy Design**: You're the data editor and reporters will use this Datasette
      instance to find stories. Design a search strategy:
      - Which columns should have full-text search enabled?
      - What "saved queries" would you pre-create for common reporter needs?
      - How would you train reporters to use search effectively?

   2. **Enrichment Pipeline**: Design an enrichment workflow for grant data:
      - What enrichments would add the most journalistic value?
      - In what order would you apply them?
      - How would you validate that enrichments are accurate?
      - What are the risks of automated enrichment (e.g., geocoding errors)?

   3. **API Applications**: Datasette's automatic API opens many possibilities. Design TWO applications:

      **Application 1 - Internal newsroom tool**:
      - What would it do? (grant lookup, alert system, comparison tool?)
      - What API endpoints would it use?
      - Who would use it and how?

      **Application 2 - Public-facing feature**:
      - How would your news website use this API?
      - What would readers be able to do? (search grants, look up their town?)
      - How would you handle rate limiting and abuse?
      - What caching strategy would you use?

   4. **Publication Ethics**: Datasette makes it easy to publish data AND an API. Consider:
      - Should all grant recipient names be searchable? Why or why not?
      - What if someone builds a tool on your API that you didn't anticipate?
      - How do you balance transparency with potential for misuse?
      - What documentation should accompany your API?
      - Should you require API keys or allow anonymous access?

   When you understand search, enrichments, AND API capabilities, move on to Act 3!

*********************
Act 3: Exploring Data for Stories
*********************

.. admonition:: 🤔 Interactive Checkpoint
   :class: tip

   **Before you begin Act 3**: This is where journalism happens! Discuss with your AI assistant:

   - What makes data "newsworthy"?
   - What questions would an editor ask about Maryland grant spending?
   - How do you distinguish a story from just an interesting fact?

In this final act, we will explore Datasette's features through the lens of finding stories - using facets, filters, SQL queries, and the features we've enabled.

.. admonition:: 🔍 Guided Exploration: Finding Stories
   :class: tip

   **Phase 1: The Reporter's Toolkit**

   Work with your AI assistant to master features that help find stories:

   1. **Facets for Pattern Discovery**:
      - Apply facets to "Fiscal Year", "Grant Category", and "Grantee" simultaneously
      - Facets show you the SHAPE of the data - where is the money concentrated?
      - Which combinations reveal unexpected patterns?

   2. **Filters for Hypothesis Testing**:
      - Create a compound filter: grants > $5M AND Category contains "education"
      - Filters let you test hunches: "I wonder if..."
      - Export filtered results for deeper analysis

   3. **Full-Text Search for Leads**:
      - Search for organization names mentioned in recent news
      - Search for politically connected terms
      - Use search to find needles in the haystack

   4. **SQL for Precision Questions**:

      .. code-block:: sql

          -- Who are the biggest repeat recipients?
          SELECT Grantee, COUNT(*) as grant_count, SUM(CAST(Amount AS REAL)) as total
          FROM grants
          GROUP BY Grantee
          HAVING grant_count > 10
          ORDER BY total DESC;

          -- What's the trend in average grant size?
          SELECT "Fiscal Year",
                 COUNT(*) as grants,
                 AVG(CAST(Amount AS REAL)) as avg_amount,
                 MAX(CAST(Amount AS REAL)) as max_amount
          FROM grants
          GROUP BY "Fiscal Year"
          ORDER BY "Fiscal Year";

          -- Find potential outliers (grants much larger than category average)
          SELECT g.*,
                 (SELECT AVG(CAST(Amount AS REAL)) FROM grants WHERE "Grant Category" = g."Grant Category") as category_avg
          FROM grants g
          WHERE CAST(g.Amount AS REAL) > 5 * (SELECT AVG(CAST(Amount AS REAL)) FROM grants WHERE "Grant Category" = g."Grant Category")
          ORDER BY CAST(g.Amount AS REAL) DESC;

.. admonition:: 🎯 Deep Dive Challenge: Investigative Data Journalism
   :class: warning

   **Challenge 1: The Story Pitch**

   Find THREE potential news stories in this data. For each, provide:

   - **Headline**: A compelling one-line summary
   - **Nut graf**: 2-3 sentences explaining why this matters
   - **The evidence**: The exact query or filter that reveals this pattern
   - **The questions**: What would you need to verify before publishing?
   - **The sources**: Who would you call for comment?

   Story angles to consider:
   - **Concentration**: Is grant money going to the same organizations repeatedly?
   - **Trends**: Has funding shifted between categories over time?
   - **Outliers**: Are there unusually large or small grants that warrant explanation?
   - **Gaps**: Who ISN'T getting grants? What areas are underfunded?
   - **Timing**: Do grant patterns correlate with political events?

   **Challenge 2: The Accountability Query**

   Write SQL to answer these accountability journalism questions:

   .. code-block:: sql

       -- Which grantees appeared for the first time in the most recent year?
       -- (New recipients could be a story)

       -- Which grantees received funding in every single year?
       -- (Long-term relationships could be a story)

       -- Which grant categories saw the biggest percentage increase/decrease?
       -- (Shifting priorities could be a story)

       -- Are there any grantees with very similar names?
       -- (Potential duplicates or related entities)

   **Challenge 3: The Enterprise Story**

   Design a larger investigative project using this data as a starting point:

   - What ADDITIONAL data would you need to request via FOIA/public records?
   - What human sources would you develop?
   - What other databases could you cross-reference (campaign finance, lobbying, corporate records)?
   - What's your timeline and publication plan?
   - How would you bulletproof your methodology?

   **Challenge 4: The Public Service Tool**

   Design how you would publish this Datasette for public use:

   - What metadata and documentation would you add?
   - What "canned queries" would help non-technical users?
   - How would you explain the data's limitations?
   - What would your "how to use this tool" guide look like?

.. admonition:: 🚀 Extension Activities
   :class: tip

   **For Advanced Students** - Choose at least ONE:

   1. **Geographic Analysis**: If your data has location information:
      - Install ``datasette-cluster-map`` for mapping
      - Or use enrichments to geocode addresses
      - What geographic patterns emerge?

   2. **Time-Series Analysis**: Use SQL window functions:
      - Calculate year-over-year percentage changes
      - Identify acceleration or deceleration in funding
      - Find recipients whose funding changed dramatically

   3. **Network Analysis**: Think about relationships:
      - Do any grantees share addresses or leadership?
      - Are there patterns in which agencies give to which recipients?
      - How would you visualize these connections?

   4. **Collaboration Setup**: Make this newsroom-ready:
      - Create a metadata.yaml with column descriptions
      - Set up saved queries for common reporter needs
      - Write documentation for your colleagues

When we're done, you can add your changes to your GitHub repository and push them:

.. code-block:: bash

    $ git add .
    $ git commit -m "Completed Datasette tutorial with analysis"
    $ git push origin main

.. admonition:: 🎉 Final Capstone Assignment
   :class: important

   **Capstone: Your Data Project Proposal**

   Prepare a complete proposal for a data journalism project using these tools.
   This should be something you could actually pursue. Discuss with your AI assistant:

   **Part 1: The Data**
   - What dataset will you use? (Be specific - provide URLs if possible)
   - Why is this newsworthy NOW?
   - What's the source and how reliable is it?
   - What are the limitations you'll need to acknowledge?

   **Part 2: The Questions**
   - What are your three main research questions?
   - What would a "home run" finding look like?
   - What would a "base hit" finding look like?
   - What's your null hypothesis (what if there's no story)?

   **Part 3: The Method**
   - What sqlite-utils commands will you use to load/explore?
   - What Datasette features (search, facets, enrichments) will help?
   - What SQL queries will test your hypotheses?
   - What additional data might you need to join?
   - How will you handle ongoing data updates?
   - Will you expose an API? For whom?

   **Part 4: The Journalism**
   - Who are your human sources?
   - What documents would you request?
   - How will you verify what the data suggests?
   - What's your publication format (story, interactive, database)?

   **Part 5: The Ethics**
   - Who could be harmed by this story?
   - What privacy considerations apply?
   - How will you give subjects opportunity to respond?
   - What context is essential to include?

.. admonition:: 📝 Comprehensive Reflection
   :class: important

   **Skills Checklist** (Verify you can explain each):

   □ sqlite-utils: insert data, run queries, export formats
   □ Data maintenance: append updates, handle duplicates, validate changes
   □ Datasette: serve, explore with facets and filters, SQL interface
   □ Full-text search: when and how to use it
   □ Enrichments: what they do and when they're appropriate
   □ Datasette API: JSON endpoints, query parameters, building on the API
   □ Story-finding: patterns, outliers, trends, gaps
   □ Verification: what questions to ask before publishing

   **Critical Thinking Questions**:

   1. **Limitations**: What stories CAN'T you find with this data? What's missing?

   2. **Context**: Raw numbers rarely tell the full story. What context would you
      need to add to any findings from this data?

   3. **Verification**: You find a surprising pattern. Walk through the steps you'd
      take before telling your editor you have a story.

   4. **Sustainability**: You publish this Datasette with an API. Six months later:
      - How do you keep the data fresh?
      - What if the state changes their data format?
      - What if external applications depend on your API?
      - How do you communicate updates and changes to users?

   5. **API Responsibility**: External developers build tools on your API:
      - What happens if you need to change the data structure?
      - How do you handle versioning?
      - What are your obligations to API consumers?
      - When might you need to restrict access?

   6. **Audience**: How would you explain your methodology to:
      - Your editor?
      - A general reader?
      - A developer who wants to use your API?
      - A subject of your story who disputes your findings?

   **Continue Learning**:

   - Explore more Datasette instances: https://datasette.io/examples
   - Read the documentation: https://docs.datasette.io/
   - Join the community: https://datasette.io/discord
   - Browse plugins: https://datasette.io/plugins

   The tools you've learned are the foundation of modern data journalism.
   The real skill is knowing what questions to ask. Keep exploring!
