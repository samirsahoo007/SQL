# How to setup postgresql on Mac

$ brew install postgresql

$ brew install gdal					                # for ogr2ogr

$ brew services start postgresql	          # Start the service; restart at login

OR

$ pg_ctl -D /usr/local/var/postgres start   # if you don't want/need a background service

$ psql postgres	                            # Connect to the default postgres database to check whether it's setup correctly

$ createdb my_test_db	                      # Create new database

$ psql my_test_db                           # Connect to the database


# Few Useful commands:

\l			# List available databases

\dt			# List available tables

\d table_name	# Describe a table

\dn			# List available schema

\df			# List available functions


# import your CSV into postgis

$ ogr2ogr -f PostgreSQL PG:"host=localhost port=5432 user=<username> dbname=<dbname> password=<password>" -oo AUTODETECT_TYPE=YES /path/to/docs.csv

$ ogr2ogr -f PostgreSQL PG:"host=localhost port=5432 user=<username> dbname=<dbname> password=<password>" --config PG_USE_COPY YES -nln $SCHEMA.<table_name> /path/to/docs.csv


# Create indexes

```
INDEX_FIELDS="$(head -n 1 /path/to/docs.csv)"

for field in $(echo $INDEX_FIELDS | tr ',' '\n')
  do
      echo $field
      index_sql="CREATE INDEX ON public.<table_name> ($field)"
      echo "Creating index: $index_sql"
      psql -d <db_name> -c "$index_sql"
  done
  ```

## Import CSV into table t_words

COPY t_words FROM '/path/to/file.csv' DELIMITER ',' CSV;

* You can tell quote char with QUOTE and change delimiter with DELIMITER

## Import CSV into table t_words, telling what columns to use

COPY t_words("name", "count", "def") FROM 'file.csv' DELIMITER ',' CSV; 

## Export table to a CSV file

COPY t_words TO 'file.csv' DELIMITER ',' CSV HEADER; 

## Export custom results to a CSV file

COPY (SELECT word, def FROM t_words) TO 'file.csv' CSV;


## Script to load csv file into new postgres table
```
#!/usr/bin/env python

import csv, re
from subprocess import call

infile = '/tmp/hrholiday-lists/TulsaCustomerData.csv'
db = 'kyl'
table = 'hrholiday_tulsa_csv'

fh = csv.reader(open(infile, 'r'), delimiter=',', quotechar='"')
headers = fh.next()

def variablize(text, prefix=''):
    if not prefix:
        # if no prefix, move any digits or non-word chars to the end
        parts = re.match('(^[\W\d]*)(.*$)', text).groups()
        text = "%s %s" % (parts[1], parts[0])
    text = ("%s %s" % (prefix, text)).strip().lower()
    text =  re.sub('[\W]', '_', text)
    return re.sub('_*$', '', text)

columns = map(variablize, file(infile).readline().split(','))
columns = map(lambda v: '%s varchar(128)' % v, columns)
queries = [
    'drop table %s;' % table,
    'create table %s (%s);' % (table, ','.join(columns)),
    "copy %s from '%s' with csv header;" % (table, infile),
    'alter table %s add column id serial;' % table,
    'alter table %s add primary key (id);' % table,
]
for q in queries:
    call(['psql','-a','-d',db,'-c',q])
```
