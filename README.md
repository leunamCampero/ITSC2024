# README

This repository explains how to reproduce the extraction of data from OpenStreetMap and use it to generate bike networks.
The main part aims at extracting data from PBF files and integrating it into a PostgreSQL database, then generating geojson files.
The resulting data is then used to generate a map, as shown in the example notebook.

## 1 - Preparing database

- Install PostgreSQL https://www.postgresql.org/ 
    - Install PostGIS extension and ODBC driver
- Add the psql.exe file directory to PATH if not already done (in `/bin`)
- Open a command terminal. Log in pgsql with command `psql -U <username>` and type your password
- Run the following commands 
    - `CREATE ROLE osmuser LOGIN PASSWORD 'osmuser';` \ create user
    - `CREATE DATABASE osm WITH ENCODING 'UTF8';` \ create database
    - `GRANT ALL PRIVILEGES ON DATABASE osm TO osmuser;` \ grant rights to user (may be too much, you can try to give less privileges)
    - `ALTER DATABASE osm OWNER TO osmuser;` \ make user owner
    - `\c osm` \ connect to database
    - `CREATE EXTENSION postgis;` \ must be done as superuser (postgres)
    - `CREATE EXTENSION hstore;` \ same
- Your database is now setup for the OSM transfer. You can use a GUI such as pgAdmin or DBeaver to visualise data.

## 2 - OSM to PGSQL

- Install or download osm2pgsql https://osm2pgsql.org/doc/install.html 
- If existing data is in the database, it seems that `--create` flag will make a diff with the data. Replace this flag by `--append` if you want to append data. If you want to remove everything, you can `DROP SCHEMA public CASCADE;` in the database and recreate it (and also recreate the extensions for this schema. You may have to connect as superuser postgres). 
- Follow instructions on https://osm2pgsql.org/doc/manual.html#running-osm2pgsql. You can also prepare the database as written in Step 3 of the doc. These instructions are summarized hereafter.
- Download an OSM file (for example here https://download.openstreetmap.fr/extracts/europe/france/rhone_alpes/)
- To build the table, the converter needs a style file. Default one already exists. For bikes we will use the `bicycle.style`. Comment line 
- Run the following command in the osm2pgsql folder
    - `./osm2pgsql.exe --create --slim <path/to/osm/pbf> --database=osm --user=osmuser -W --host=localhost --port=5432 --style=<path/to/style/file>`

## 3 - Prepare extraction

- Download or clone the Geovelo repository to have the sql files https://gitlab.com/geovelo-public/requetes_amenagements_cyclables.
- Put it in a folder next to `run.py` script.

## 4 - Run code

Run the `run.py` file to generate a shell instruction. Paste it into a command terminal in the current folder (by default it will execute automatically), it will generate a CSV extract. The structure is the following (`i` is even):
- Line `i`: name of file according to some formatting
- Line `i+1`: geojson file


# Other tips

#### Start PostgreSQL server

- `pg_ctl -D "<path/to/postgres/installation>\data" restart`
