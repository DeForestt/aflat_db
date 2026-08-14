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
let database = (await db.createDatabase("example.db"))!;

let columns = [
    new ColumnSchema(`id`, db_type.Int(), false),
    new ColumnSchema(`name`, db_type.Text(), false),
    new ColumnSchema(`active`, db_type.Bool(), false),
];

let schema = new TableSchema(`users`, $columns);
let users = (await database.createTable($schema))!;

let row = new Row([
    db_value.Int(1),
    db_value.Text(`James`),
    db_value.Bool(true),
]);

let __id = (await users.insert(row))!;
database.close();
```

Open an existing database and look up a table by name:

```aflat
let database = (await db.openDatabase("example.db"))!;
let users = (await database.table(`users`))!;
database.close();
```

See `examples/basic.af` for the complete create, insert, reopen, and scan flow.

## Development

Run the library test suite with:

```bash
aflat test
```

The suite covers database creation, persistence, paging, table heaps, schemas,
typed row codecs, catalogs, and scans.
