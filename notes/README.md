# Setup

Credits: [Phil Eaton](https://github.com/eatonphil)

```sh
$ ./configure --prefix=$(pwd)/test-install
$ make -j8 && make install
$ ./test-install/bin/initdb $(pwd)/test-data # Create a database
$ echo "port=8080" > $(pwd)/postgres.conf # This is the minimal postgres run config
$ mkdir test-run # Place for postgres to put lock files
$ ./test-install/bin/postgres --config-file=$(pwd)/postgres.conf -D $(pwd)/test-data -k $(pwd)/test-run
```

If you change the system catalog (`.dat` files), you would need to re-initalize the DB:

```sh
$ rm -rf test-data
$ ./test-install/bin/initdb $(pwd)/test-data # Re-create the database
```

Else, you'd face this error when you try to use the `color` type:

```sh
error: type 'color' does not exist.
```

# How to test changes?

Run Postgres:

```sh
$ ./test-install/bin/postgres --config-file=$(pwd)/postgres.conf -D $(pwd)/test-data -k $(pwd)/test-run
```

Run `psql`:

```sh
$ ./test-install/bin/psql -p 8080 -h localhost -d postgres
```

A few tests:

```sql
postgres=# CREATE TABLE color (val color);
CREATE TABLE
postgres=# INSERT INTO color VALUES ('FF0000');
INSERT 0 1
postgres=# SELECT * FROM color;
  val
--------
 FF0000
(1 row)

postgres=# SELECT val+'00FF00' FROM color;
 ?column?
----------
 FFFF00
(1 row)

postgres=# SELECT 'FF0000'::color + '00FF00'::color;
 ?column?
----------
 FFFF00
(1 row)

postgres=# SELECT * FROM color WHERE val<>'0000FF';
  val
--------
 FF0000
(1 row)

postgres=# SELECT * FROM color WHERE val='0000FF';
 val
-----
(0 rows)
```