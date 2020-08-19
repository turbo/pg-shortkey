# pg-shortkey

This installs a trigger and type which allow you to use YouTube-like short IDs (e.g. `1TNhBqYo-6Q`) as Postgres Primary Keys.

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
