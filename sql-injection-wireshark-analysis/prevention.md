#  Preventing SQL Injection Attacks

SQLi is still one of the top threats (see OWASP Top 10). Here's how to prevent it:

## 1. Use Parameterized Queries (Prepared Statements)

❌ Vulnerable:
```sql
"SELECT * FROM users WHERE name = '" + userInput + "';"

cursor.execute("SELECT * FROM users WHERE name = ?", (userInput,))

2. Input Validation
Whitelist acceptable inputs

Reject suspicious characters (', ", ;, --, etc.)

3. Least Privilege
DB accounts used by the app should have read-only or limited access.

Never use root for applications.

4. Use a WAF
Web Application Firewalls help block common SQLi payloads.

 5. Monitor and Log
Set up logging to detect repeated failed login attempts or strange inputs.

