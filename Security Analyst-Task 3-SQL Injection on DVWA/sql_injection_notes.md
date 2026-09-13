# SQL Injection Notes

## Test Environment

- Application: DVWA
- Security Level: Low
- Target: `127.0.0.1:42001`
- SQL Injection Module: DVWA SQL Injection
- Environment: Local Kali Linux virtual machine

## Baseline Test

The initial input was:

```text
1
```

The application returned:

```text
ID: 1
First name: admin
Surname: admin
```

This confirmed that the SQL Injection module was functioning normally before testing the vulnerability.

## SQL Injection Test 1

### Payload

```text
1' OR '1'='1' #
```

### Result

The application returned multiple database records instead of only the single requested record.

The returned records included:

- Admin — Admin
- Gordon — Brown
- Hack — Me
- Pablo — Picasso
- Bob — Smith

### Analysis

The payload changes the intended SQL condition so that the condition evaluates as true for multiple records. The `#` character comments out the remainder of the SQL statement.

This demonstrates that the application is vulnerable to SQL Injection because user input is being incorporated into the SQL query without adequate parameterization.

## SQL Injection Test 2

### Payload

```text
1' OR 1=1 #
```

### Result

The application again returned multiple database records.

### Analysis

The expression `1=1` is always true. Combined with the `OR` operator, it causes the query condition to evaluate as true for the returned rows.

The `#` character comments out the remaining part of the original query.

## Evidence

### Screenshot 1

`01_sql_injection_payload_1.png`

Shows the first SQL Injection payload and the resulting multiple records.

### Screenshot 2

`02_sql_injection_payload_2.png`

Shows the second SQL Injection payload and the resulting multiple records.

### Screenshot 3

`03_dvwa_security_low.png`

Shows that the DVWA security level was configured to Low.

## Security Impact

Successful SQL Injection can allow unauthorized access to database records and may expose information that the application was not intended to return.

Depending on the application's implementation and database privileges, SQL Injection can potentially lead to:

- Unauthorized data access
- Sensitive information disclosure
- Authentication bypass
- Data modification or deletion

## Prevention

SQL Injection should be prevented by using parameterized queries or prepared statements.

Example:

```php
$stmt = $conn->prepare("SELECT first_name, last_name FROM users WHERE user_id = ?");
$stmt->bind_param("i", $user_id);
$stmt->execute();
```

User input should also be validated, database accounts should use least-privilege permissions, and detailed database errors should not be exposed to users.

## Ethical Testing

All tests were performed against the intentionally vulnerable DVWA application running locally on my own Kali Linux virtual machine.

No real websites or unauthorized systems were tested.