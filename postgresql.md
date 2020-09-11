# How to setup postgresql on Mac

$ brew install postgresql

$ brew install gdal					      # for ogr2ogr

$ brew services start postgresql	# Start the service

$ psql postgres	                  # Connect to the default postgres database to check whether it's setup correctly

$ createdb my_test_db	            # Create new database

$ psql my_test_db                 # Connect to the database


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
