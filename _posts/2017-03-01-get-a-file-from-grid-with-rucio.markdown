---
layout: post
title: Get a file from Grid with 'rucio'
date: '2017-03-01 12:15:41'
---

1. Setup the ATLAS environment and the rucio tools:

    ```setupATLAS
    lsetup rucio
    ```

1. Look for the collection on the Grid, specifying a scope (`user:username`) and a pattern:

    ```
    rucio list-dids user.username:user.username.EventPicking.RAW.part.01_event.dat.\*
    ```

    You will get a list of matching collections:

    ```
    | user.username:user.username....113967521 | DATASET |
    | user.username:user.username....113967512 | DATASET |
    | user.username:user.username....113943566 | DATASET |
    | user.username:user.username....113967555 | DATASET |
    ```

2. You can dowload the whole collection with this command:

    `rucio get user.username:user.username....113967521`

2. Also,  you can see the content of one particular collection:

    ```
    rucio list-content user.username:user.username....113967521
    ```
    
    You will get a list of files in the collection:

    ```
    | user.username:...10378332._000016.event.dat | FILE |
    | user.username:...10378332._000017.event.dat | FILE |
    | user.username:...10378332._000018.event.dat | FILE |
    | user.username:...10378332._000019.event.dat | FILE |
    ...
    ```

3. and you can get a single file from the collection:

    ```
    rucio get user.username:user.username.10378332._000016.event.dat
    ```


