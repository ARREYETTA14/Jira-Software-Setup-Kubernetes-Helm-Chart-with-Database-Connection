# You can grant permission to the public schema by running the following commands: 

1. Create the user that Jira will be using.

```sh
postgres=# CREATE USER atlas WITH PASSWORD 'atlas';
```

2. Create the database.

```sh
postgres=# CREATE DATABASE atlas WITH ENCODING 'UNICODE' LC_COLLATE 'C' LC_CTYPE 'C' TEMPLATE template0;
```

3. Grant the necessary privileges to the database:

```sh
postgres=# GRANT ALL PRIVILEGES ON DATABASE atlas TO atlas;
```

4. Connect to the database.

```sh
postgres=# \c atlas
```

You are now connected to database atlas as user postgres.

5. Grant the required schema privileges:

```sh
atlas=# GRANT ALL ON SCHEMA public TO atlas;
```
