## How to backup PostgreSQL Data

There are three approaches:

- SQL dump
- File system level backup
- Continuous archiving

### SQL Dump

It backs up a single database at a time. It generates a file with SQL commands that, when fed back to the server, will recreate the database in the same state as it was at the time of the dump.

Exampe (you must log in to the db server as postgres):

```
pg_dump dbname > dumpfile
```

Above command generates a text file, but you can specify the file extension, like `dumpfile.sql`.

The command does not have access privileges, so you must run it as a database superuser.

Options/switches:

```
-n schema (specify the schema)
-t table (specify the db table)
-h host (specify the host)
-p port (specify the port)
-F c (specify custom file extension, e.g. .txt, .pdf, .docx)
-F t (specifies a .tar format)
-D d (specify a directory to save the dump file)
```

### Backing up remote Database

```
pg_dump -U dbuser -h dbhost -p 5432 dbname > dumpfile
```

With the above command, replace dbuser with the correct db user and dbhost with the db host

### Dumping from one server to another

You can dump a database from one server to another with this command:

```
pg_dump -h host1 dbname | psql -h host2 dbname
```

### Auto Backup using a Cron Job

`Step1:` Create a backup directory

```
mkdir -p srv/backups/databases
```

`Step2:` Edit the crontab, add a cron job that runs every day at midnight

```
crontab -e
0 0 * * *  pg_dump  -U postgres dbname > /srv/backups/postgres/dumpfile
```

## Backing Up a Dockerized PostgreSQL Database

`Step1:` Create a backup directory, at a location which is shared by the container's filesystem and the host machine,

```
docker exec -t pg-db bash -c 'mkdir /var/lib/postgresql/data/backups'
```

where `pg-db` is the container name

`Step2:` Create the backup

```
docker exec -t pg-db bash -c 'pg_dump dbname -U postgres  --file=/var/lib/postgresql/data/backups/dumpfile-$(date +%Y-%m-%d).sql'

```

### Restoring the backup

```
psql dbname < dumpfile
```

The above command restores a textfile (.txt). Before running this command, first create the database. Here mis an example command to create a database, using `template0`:

```
createdb -T template0 dbname
```

Non-text files are restored using the `pg_restore` utility.

```
pq_restore -d dbname dumpfile.docx
pg_restore -d dbname dumpfile.tar
pq_restore -d dbname dumpfile.dir
```

Options/switches:

```
psql --set ON_ERROR_STOP=on dbname < dumpfile (exits with status 3 on error)
```

To backup the entire contents of a database cluster, `pg_dumpall` is used. It backs up each database in a given cluster, and also preserves cluster wide data such as roles and tablespace definitions.

`pg_dumpall` works by emitting commands to re-create roles, tablespaces, and empty databases, then invoking `pg_dump` for each database.

```
pg_dumpall > dumpfile
```

The resulting dump can be restored with psql:

```
psql -f dumpfile postgres
```

[Read More](https://www.postgresql.org/docs/current/backup-dump.html)

[File System Backup](https://www.postgresql.org/docs/current/backup-file.html)

[Continuos Archiving Backup](https://www.postgresql.org/docs/current/continuous-archiving.html)
