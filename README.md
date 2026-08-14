# aflat_db

An embeddable database library for AFlat. It provides typed table schemas,
row insertion and scanning, persistent catalogs, slotted pages, and async file
I/O.

## Install

Install the Git repository into an AFlat application under the `aflat_db`
module name:

```bash
aflat --name aflat_db install <git-repository-url>
```

The URL must end in `.git` for the current AFlat package manager.

## Import

Import the high-level database API and the value/schema types needed by your
application:

```aflat
import {createDatabase, openDatabase} from "./aflat_db" under db;
import TableSchema, ColumnSchema from "./aflat_db/Schemas";
import {Int, Text, Bool} from "./aflat_db/Schemas/Types" under db_type;
import DBValue from "./aflat_db/DBValue";
import {Int, Text, Bool} from "./aflat_db/DBValue" under db_value;
import Row from "./aflat_db/Row";
```

Create a database and a typed table:

```aflat
let database = (await db.createDatabase("example.db"))
    .expect("unable to create database");

let columns = [
    new ColumnSchema(`id`, db_type.Int(), false),
    new ColumnSchema(`name`, db_type.Text(), false),
    new ColumnSchema(`active`, db_type.Bool(), false),
];

let schema = new TableSchema(`users`, $columns);
let users = (await database.createTable($schema))
    .expect("unable to create users table");

let row = new Row([
    db_value.Int(1),
    db_value.Text(`James`),
    db_value.Bool(true),
]);

let id = (await users.insert(row))
    .expect("unable to insert row");

let updated_row = new Row([
    db_value.Int(1),
    db_value.Text(`James Thompson`),
    db_value.Bool(true),
]);

(await users.update(id, updated_row))
    .expect("unable to update row");

let stored = (await users.read(id))
    .expect("unable to read updated row");
database.close();
```

Open an existing database and look up a table by name:

```aflat
let database = (await db.openDatabase("example.db"))
    .expect("unable to open database");
let users = (await database.table(`users`))
    .expect("unable to find users table");
database.close();
```

`update` keeps the row's record ID stable and validates the replacement row
against the table schema. See `examples/basic.af` for the complete create,
insert, reopen, and scan flow.

## Development

Run the library test suite with:

```bash
aflat test
```

The suite covers database creation, persistence, paging, table heaps, schemas,
typed row codecs, updates, catalogs, and scans.
