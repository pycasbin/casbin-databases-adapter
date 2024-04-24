# Databases Casbin Adapter

[![GitHub Actions](https://github.com/pycasbin/casbin-databases-adapter/workflows/build/badge.svg?branch=master)](https://github.com/pycasbin/casbin-databases-adapter/actions)
[![Coverage Status](https://coveralls.io/repos/github/pycasbin/casbin-databases-adapter/badge.svg?branch=master)](https://coveralls.io/github/pycasbin/casbin-databases-adapter?branch=master)
[![Version](https://img.shields.io/pypi/v/casbin_databases_adapter.svg)](https://pypi.org/project/casbin_databases_adapter/)
[![PyPI - Wheel](https://img.shields.io/pypi/wheel/casbin_databases_adapter.svg)](https://pypi.org/project/casbin_databases_adapter/)
[![Pyversions](https://img.shields.io/pypi/pyversions/casbin_databases_adapter.svg)](https://pypi.org/project/casbin_databases_adapter/)
[![Download](https://img.shields.io/pypi/dm/casbin_databases_adapter.svg)](https://pypi.org/project/casbin_databases_adapter/)
[![License](https://img.shields.io/pypi/l/casbin_databases_adapter.svg)](https://pypi.org/project/casbin_databases_adapter/)

This is an Adapter for [PyCasbin](https://github.com/casbin/pycasbin) that implemented using [Databases](https://www.encode.io/databases) connection to achieve async process

## Installation

```
pip install casbin_databases_adapter
```

## Simple Example

```python
import casbin_databases_adapter
import casbin
from databases import Database
import sqlalchemy
from sqlalchemy import Table, Column, String, Integer
from sqlalchemy.sql.ddl import CreateTable
import asyncio

DATABASE_URL = "sqlite+aiosqlite:///example.db"

async def create_casbin_rule_table(db: Database):
    metadata = sqlalchemy.MetaData()
    table = Table(
        "casbin_rules",
        metadata,
        Column("id", Integer, primary_key=True),
        Column("ptype", String(255)),
        Column("v0", String(255)),
        Column("v1", String(255)),
        Column("v2", String(255)),
        Column("v3", String(255)),
        Column("v4", String(255)),
        Column("v5", String(255)),
    )
    q = CreateTable(table)
    await db.execute(query=str(q))
    return table

async def main():
    database = Database(DATABASE_URL)
    await database.connect()
    casbin_rule_table = await create_casbin_rule_table(database)
    adapter = casbin_databases_adapter.DatabasesAdapter(db=database, table=casbin_rule_table)
    
    e = casbin.Enforcer('path/to/model.conf', adapter)
    
    sub = "alice"  # the user that wants to access a resource.
    obj = "data1"  # the resource that is going to be accessed.
    act = "read"  # the operation that the user performs on the resource.
    
    if e.enforce(sub, obj, act):
        # permit alice to read data1
        pass
    else:
        # deny the request, show an error
        pass

# run the main function
asyncio.run(main())
```

### Getting Help

- [PyCasbin](https://github.com/casbin/pycasbin)

### License

This project is licensed under the [Apache 2.0 license](LICENSE).