# A set of useful functions for making and manipulating Athena databases

### Instalation and Requirments
You must have python >= 3.8, all other requirements can be the `pyptoject.toml`.
Currently, the package must be installed from GitHub, this can be done with the following command:
`pip install git+https://github.com/moj-analytical-services/AthenaTools.git`

## Creating a single table

to create a single table (or single table object):
```python
from AthenaTools.AthenaTools import AthenaTable

MyTable =  AthenaTable()

# There are 4 required attributes for any table
MyTable.table_name = "my_table"
MyTable.db_name = "my_db"
MyTable.db_base_path = "s3://alpha-bucket/folder/"
MyTable.data_path = "s3://alpha-bucket/data/file.csv"
```
optionally, these can also be set as key-word arguments (or a mix of the two)
```python
from AthenaTools.AthenaTools import AthenaTable

MyTable = AthenaTable(table_name="my_table", db_name="my_db")
MyTable.db_base_path = "s3://alpha-bucket/folder/"
MyTable.data_path = "s3://alpha-bucket/data/file.csv"
```

These attributes stay largely the same throughout the other classes in this module.

to create the table in Athena:
```python
MyTable.create_table()
```

Tables can also be deleted. Either just the schema (leaving the data intact), or data as well
```python
# deleting just schema only
MyTable.delete_table()

# deleting data as well
MyTable.nuke()
```

## Creating a database of tables

To manage a logical database of tables, you create an `AthenaDatabase` object. This object
can have table objects added to it (as created above) or create tables with the same attributes.
As with the table object, all attributes can be set with either dot notation or as keyword arguments.
We will add two tables to the `AthenaDatabase` object with two methods, first by adding an existing `AthenaTable` object
and second by creating a table from the `AthenaDatabase`'s attributes

```python
from AthenaTools.AthenaTools import AthenaDatabase, AthenaTable

MyTable = AthenaTable(table_name="my_table_1", db_name="my_db")
MyTable.db_base_path = "s3://alpha-bucket/folder/"
MyTable.data_path = "s3://alpha-bucket/data/file.csv"

# these are the two required attributes for the AthenaDatabase
MyDb = AthenaDatabase(db_name="my_db")
MyDb.db_base_path = "s3://alpha-bucket/folder/"

# once a table object is set like this, the way to interact with it is via dot notation
MyDb.add_table(MyTable)
MyDb.my_table_1.table_name # "my_table_1"

MyDb.add_table("my_table_2")
MyDb.my_table_2.table_name # "my_table_2"

# to create all the tables in this database:
MyDb.create_all_tables()

# to delete all the schemas only:
MyDb.delete_all_tables()

# to delete all schemas and data:
MyDb.nuke_all()
```

Each individual table can be accessed (attributes changed, or tables deleted etc):
```python
MyDb.my_table_1.delete_table()
```

You can also manage many database objects from the `Athena` object

```python
from AthenaTools.AthenaTools import Athena, AthenaDatabase

MyAthena = Athena()
MyAthena.add_db("my_db_1")

MyDb2 = AthenaDatabase(db_name="my_db_2", db_base_path = "s3://alpha-bucket/db_2/")
MyAthena.add_db(MyDb2)

MyAthena.my_db_1.db_base_path = "s3://alpha-bucket/db_1/"
```
Interaction with the `my_db_1` and `my_db_2` objects is the same as above as they are
`AthenaDatabase` objects. To create all tables, delete all tables (data or schemas only) from
the `Athena` object:

```python
# create all tables:
MyAthena.create_all_tables()

# delete all schemas:
MyAthena.delete_all_tables()

# delete all tables (data as well)
MyAthena.nuke_all()
```
