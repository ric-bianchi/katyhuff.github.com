---
layout: post
title: Import the persistified GeoModel to the Neo4j graph DB
---

If you like, you can inspect the GeoModel SQLite DB with the SQLite command-line tool, like for example: 

    sqlite3 geo_example_toyFactory.db
    .tables
    SELECT * FROM LogVols;


or with a GUI like the [open source "DBeaver"](http://dbeaver.jkiss.org/).

# Step 1 - SQlite tables to CSV

First of all, we have to export the GeoModel SQLite file to CSV files


A script to export all tables to corresponding CSV files:

    --
    -- Export tables
    --
    .headers on
    .mode csv
    .output SerialTransformers.csv
    SELECT id, funcId, volId, volTable, copies FROM SerialTransformers;
    .output Functions.csv
    SELECT id, expression FROM Functions;
    
    
    .quit

(maybe put it on ghist???)

# Step 2 - Install Neo4j

Mac OS X, with 'brew':  `brew install neo4j --with-neo4j-shell-tools`

Now you can launch Neo4j with: `neo4j start`. 

After starting it, you can find it at this address in your browser: http://localhost:7474/.
The first time you open it, you can connect to a new database with the default user/password: neo4j/neo4j. Then you will be asked to set a new password.

When you install Neo4j, files are stored in different places according to the installation method you have used:

On Mac OS X:

* if you install the Community Edition using the .dmg installer, files are installed under `~/Documents/Neo4j/`; in particular, the default database is installed under `~/Documents/Neo4j/default.graphdb`;
* if you installed Neo4j using "brew", the files are installed under the path `/usr/local/Cellar/neo4j`; in particular, the default database is installed under `/usr/local/Cellar/neo4j/3.1.1/libexec/data/databases/graph.db` and the binaries (like `neo4j-shell` or the start-stop command `neo4j`) are installed under `/usr/local/bin`, with symlinks from `/usr/local/bin`.



# Step 3 - Import nodes in Neo4j

First of all, let's delete the old database, to start from a pristine one. Neo4j does not have a "clear" command, but we can safely delete the database data, without using the server:

    rm -rf /Users/rbianchi/Documents/Neo4j/default.graphdb

Now, let's create a Cypher script, which we name `import_csv.cypher`. In that script, we start importing the tables' rows in Neo4j as nodes using the *PERIODIC COMMIT* and the "LOAD CSV" commands:

    // Create NameTags
    USING PERIODIC COMMIT
    LOAD CSV WITH HEADERS FROM "file:///path/to/NameTags.csv" AS row
    CREATE (:NameTag {name: row.name, id: row.id});
    ...

Then, we create the indexes:

    ...

Finally, we create the relationships between nodes:

    ...

In the end, we can execute the script through the shell command:

    neo4j-shell -path ~/Documents/Neo4j/default.graphdb -file step2_import_csv.cypher

*Note:* the `-path` options set the path of the Neo4j DB you want to import your objects into. The path above is the default one when you install Neo4j Community Edition on Mac with the 'dmg' installer.

*Note:* you have to stop the community edition machinery (which gives you the standard DB browser) when you want to execute a script through the neo4j-shell command, otherwise you get an error.

In brief:

    rm -rf /Users/rbianchi/Documents/Neo4j/default.graphdb
    neo4j-shell -path ~/Documents/Neo4j/default.graphdb -file step2_import_csv.cypher


----

*Sources*:

* http://stackoverflow.com/questions/33564859/using-where-clause-in-multiple-relationships-in-neo4j
* https://github.com/neo4j-examples/graphgists/blob/master/retail/northwind-graph.adoc
* https://neo4j.com/blog/importing-data-neo4j-via-csv/
* http://jexp.de/blog/2014/06/load-csv-into-neo4j-quickly-and-successfully/
* http://www.sqlitetutorial.net/sqlite-export-csv/
* https://neo4j.com/developer/guide-importing-data-and-etl/
* http://neo4j.com/docs/operations-manual/current/tutorial/import-tool/

