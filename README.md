# postgresql-security

## Set Up Permissions

The first thing we need to do is manage the permissions. Permissions are made with the following naming convention

p_table_read / p_table_write

For example:

p_students_read: permission to read the students table
p_teachers_read: permission to read the teachers table
p_students_write: permission to write to the students table
p_teachers_write: permission to write to the teachers table

To create a role, use `CREATE ROLE role_name;`.

### Read Permissions

Give the read roles, permission to SELECT items in their respective tables.

To grant a permission, use `GRANT PERMISSION ON table TO role`;. For example:

`GRANT SELECT ON students TO p_students_read;`

and

`GRANT SELECT ON teachers TO p_teachers_read;`

### Write Permissions

Give the roles, p_students_write and p_teachers_write, permission to SELECT, INSERT, UPDATE, and DELETE items in the students and teachers tables, respectively.

To grant multiple permissions, you can use GRANT SELECT, INSERT, UPDATE, DELETE ON table TO role;

## Set Up Groups

The convention to create group roles is g_purpose. For example, you can create group roles like the following. To create a role, use `CREATE ROLE role_name;`.

- g_school: group for the school employees
- g_district: group for the district employees

Each group role can then be assigned a set of permissions. For example:

- Grant the permission roles, p_students_read and p_teachers_read, to the group g_school.
- Grant the permission roles, p_students_write and p_teachers_write, to the group g_district.

To do so use `GRANT role TO other_role;`.

## Create User Accounts

To create roles with login, use `CREATE ROLE user_name WITH LOGIN;.`

Create three user account roles that can log in: u_principal_skinner, u_teacher_hodge, and u_it_sonia.

Add the user role u_principal_skinner to the group g_district and the user roles, u_teacher_hodge and u_it_sonia, to the group g_school.

## Default Deny

In PostgreSQL you can add default-deny permissions. Use `REVOKE PERMISSION ON table FROM PUBLIC`; to revoke a permission.

Remove all public permissions for the tables, students and teachers. For this task, you will need to run two commands:

`REVOKE ALL ON students FROM PUBLIC;`

and

`REVOKE ALL ON teachers FROM PUBLIC;`

## Configure Postgres Settings

### Listen Addresses

Next, we need to make sure the Postgres server does not accept connections from every IP address. Edit postgresql.conf to change the listen_addresses parameter so that the server only accepts connections from the school network ( localhost) and the district network (235.84.86.65).

Set the listen_addresses parameter in postgresql.conf to 'localhost, 235.84.86.65'.

listen_addresses = 'localhost, 235.84.86.65'

### Port

The server currently listens on port 5432, which is very dangerous because it’s the default Postgres port. Change the port parameter to 61342,

Hint: Set the port parameter in postgresql.conf to 61342.

port = 61342

### Enable SSL

Enable SSL on the server.

Hint: Set the ssl parameter in postgresql.conf to on.

ssl = on

## Configure Postgres Host-Based Authentication

Now that the server settings are configured, move to pg_hba.conf. This `pg_hba.conf` file is a configuration file for PostgreSQL's host-based authentication. It contains a set of rules that determine how clients are allowed to connect to the PostgreSQL database server.

Add a rule to allow members of the g_school group on the school’s local network to access the students database. SSL is not necessary. We should use SHA-256 password authentication.

Remember, the order is

`connection_type  db  user  address  auth_method  [auth_options]`

This gives us:

`host students +g_school samenet scram-sha-256`

Add another rule using the same configuration as the last rule but change the database to teachers.

`host teachers +g_school samenet scram-sha-256`

Add a rule for the principal’s account, u_principal_skinner, to access all databases from any address. Use SSL and SHA-256 password authentication.

`hostssl all u_principal_skinner all scram-sha-256`

Add a rule for the members of the school district in the group, g_district, to access all databases from the district’s network, 235.84.86.65. Use SSL and SHA-256 password authentication.

Finally, add a default-deny rule to deny all other connections.

host all all all reject

Overall, this configuration file allows users in the `g_school` group to connect as either `students` or `teachers` from hosts in the `samenet` network, while requiring SSL for connections as the `u_principal_skinner` role. All other connection attempts are rejected.

## Implement Environment Variables

You can create an environment variable using the format: NAME=VALUE in .env.

For example, your .env file could look like so...

```dotenv
# NOTE - FAKE DATA FOR DEMONSTRATION ONLY...

# Postgres API Key (students)
POSTGRES_API_KEY=gVvzJqrWLL6MXLzHeHERnKp

# District API Key (teachers)
DISTRICT_API_KEY=zsi9DeEcewB7MsgzPy2zxsp
```

These variables can be used in your scripts with the dotenv package. For example in a JavaScript file:

```JavaScript
// Import the dotenv package
import dotenv from "dotenv"

// Inject environment variables
dotenv.config()

// print API keys that are injected as environment variables.
console.log(process.env.POSTGRES_API_KEY);
console.log(process.env.DISTRICT_API_KEY);

```

## Prevent Uploading Sensitive Information

Lastly, we need to make sure the Postgres configuration files and the environment variables are not uploaded to the public repository. Using .gitignore, ignore the following files: .env, pg_hba.conf, and postgresql.conf.

.gitignore should contain:

```.gitignore
.env
pg_hba.conf
postgresql.conf
```
