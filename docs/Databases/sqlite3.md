# Useful links:
- [sqlitetutorial](https://www.sqlitetutorial.net/sqlite-commands/)

The configuation for the Flow Probe is stored in an Sqlite3 database here:
/opt/sysmaint/run/data/IPFIX.sqlite.

# SqlLite commands

- show tables:
    ```sqlite
    .tables
    ```
- select the Flow Probe config
    ```sqlite
    select * from Flow_config;
    ```
- to exit the database
    ```sqlite
    .quit
    ```
