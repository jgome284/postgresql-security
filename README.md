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
