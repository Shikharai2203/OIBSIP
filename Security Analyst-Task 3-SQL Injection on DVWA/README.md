# Task 3 — SQL Injection on DVWA

## Objective

The objective of this task was to understand and demonstrate a SQL Injection vulnerability using the Damn Vulnerable Web Application (DVWA) in a controlled local environment.

The testing was performed only against the locally hosted DVWA instance running on Kali Linux.

## Lab Environment

- Operating System: Kali Linux
- Application: Damn Vulnerable Web Application (DVWA)
- DVWA URL: `http://127.0.0.1:42001`
- DVWA Security Level: Low
- Database: MariaDB
- Testing Target: Localhost (`127.0.0.1`)

## What is SQL Injection?

SQL Injection is a web application vulnerability that occurs when user-supplied input is incorporated into an SQL query without proper validation or parameterization.

An attacker can manipulate the structure of the SQL query by supplying specially crafted input. This may allow unauthorized access to database records or other unintended database operations.

## Testing Methodology

The SQL Injection module in DVWA was configured to the **Low** security level.

A normal request was first tested using:

```text
1
```

The application returned the record for the administrator account.

Two SQL Injection payloads were then tested.

### Payload 1

```text
1' OR '1'='1' #
```

The payload caused the application to return multiple database records instead of only one record.

### Payload 2

```text
1' OR 1=1 #
```

This payload also caused multiple database records to be returned.

## Results

The successful injection demonstrated that user input was being directly incorporated into the SQL query without adequate protection.

The returned records included:

- Admin — Admin
- Gordon — Brown
- Hack — Me
- Pablo — Picasso
- Bob — Smith

The repeated injected value displayed in the ID field is a result of the manipulated query returning multiple rows.

## Security Impact

A SQL Injection vulnerability can allow an attacker to access database information that should not be directly exposed through the application.

Depending on the application's database privileges and implementation, SQL Injection may potentially lead to:

- Unauthorized access to database records
- Exposure of sensitive information
- Authentication bypass
- Modification or deletion of database data

## Prevention

The primary defense against SQL Injection is to use **parameterized queries (prepared statements)** instead of directly concatenating user input into SQL queries.

Example using PHP and MySQLi:

```php
$stmt = $conn->prepare("SELECT first_name, last_name FROM users WHERE user_id = ?");
$stmt->bind_param("i", $user_id);
$stmt->execute();
```

Additional security practices include:

- Validate and constrain user input.
- Use prepared statements or parameterized queries.
- Apply the principle of least privilege to database accounts.
- Avoid exposing detailed database errors to users.
- Use secure coding practices and regular security testing.

## Evidence

The following screenshots document the testing:

1. `01_sql_injection_payload_1.png` — First successful SQL Injection payload and returned records.
2. `02_sql_injection_payload_2.png` — Second successful SQL Injection payload and returned records.
3. `03_dvwa_security_low.png` — DVWA configured at Low security level.

## Ethical Considerations

All testing was performed against a deliberately vulnerable DVWA application running locally on my own Kali Linux virtual machine.

No real websites, external systems, or unauthorized networks were tested.

## Conclusion

The task demonstrated how insufficiently protected user input can allow SQL Injection and result in unintended database queries.

Using prepared statements, parameterized queries, input validation, and appropriate database privileges can significantly reduce the risk of SQL Injection vulnerabilities.