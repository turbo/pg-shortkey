# pg-shortkey

This installs a trigger and type which allow you to use YouTube-like short IDs (e.g. `1TNhBqYo-6Q`) as Postgres Primary Keys. Just like the YouTube IDs, SHORTKEY IDs are fixed length and URL-safe.

[Short comment on why this exists and how I use it in the thread over on Hacker News](https://news.ycombinator.com/item?id=25305502).

## Install

Execute `pg-shortkey.sql` against your DB. It will do nothing if shortkey is already installed.

## Use

Example usage:

```pgsql
CREATE TABLE test (id SHORTKEY PRIMARY KEY, name TEXT);

CREATE TRIGGER trigger_test_genid BEFORE INSERT ON test FOR EACH ROW EXECUTE PROCEDURE shortkey_generate();

-- generate
INSERT INTO test(name) VALUES ('bob'), ('joe');

-- user-supplied ID
INSERT INTO test(id, name) VALUES ('1TNhBqYo-6Q', 'lisa');

SELECT * FROM test;

-- id           name
-- 4E_z0mHJvrk  bob
-- wiz_j0HIBuQ  joe
-- 1TNhBqYo-6Q  lisa
```

`SHORTKEY` is compatible with `text` and should be handled as such by most sane ORMs. If not, create a type mapping to whatever your string type is in your app/ORM.

## Caveats

SHORTKEYs are truly random, so they fragment the index space just like v4 UUIDs, decreasing performance slightly. But unlike UUID (which provides an instance-local pseudo-sequential type via UUID v1 MC), that behavior can't be changed. This is intentional. SHORTKEYs are supposed to be used for external facing IDs, like in APIs. There, they prevent enumeration and cardinality evaluation (e.g. *how many things are there* and *what's the next/previous thing* - just like YouTube). Use where appropriate.
