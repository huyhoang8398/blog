+++
title = "Restoring Large MySQL Dump - 900 Million Rows"
date = "2023-01-12T19:15:59+0000"
authors = ["kn"]
cover = "posts/restoring-large-mysql-dump/mysql-logo2.png"
tags = ["mysql", "database", "shell"]
keywords = ["mysqldump", "database", "mysql"]
description = "Yesterday I had a fun opportunity of restoring roughly 912 Million Rows to a database. 902 Million belonged to one single table (902,966,645 rows, to be exact)"
showFullContent = false
+++

Yesterday I had a fun opportunity of restoring roughly 912 Million Rows to a database. 902 Million belonged to one single table (902,966,645 rows, to be exact).

### Problem

Our current backup system uses mysqldump. It dumps a 25 GB sql dump file, which compresses to about 2.5GB using gzip. The last time we needed to restore a backup it was only about 9GB, and it took several hours.

This time, I created the database, and from the mysql prompt I issued the following command:

```bash
. /path/to/backup/database_dump.sql
```

It would run great until it got to about 10% of the way through the scores table. However, it would start to slow down. Because the rows were so small in our scores table, each INSERT statement had about 45,000-50,000 records. So each line had roughly 1MB of data.

At first it would insert a set of 50,000 in half a second or so. However, after a few million records, it would slow down to three second, and got to about 10 seconds per INSERT statement. This was a huge problem, given that I had roughly 18,000 INSERT statements, and at 10 seconds per INSERT, it would take **50 hours** to restore. Our website was down during this restore, since it was our primary database. So being down for over two days was **not** an option.

While trying to diagnose the problem I noticed something. While using the MySQL command `show processlist` the thread for the Database Restore would be in the sleep state for 9-10 seconds, and then the query would execute in under 0.2 seconds. So it wasn’t a problem with MySQL storing the data, but a problem with reading the data from such a large database dump file.

So I tried from the server’s command line `mysql -u user_name -p database_name < /path/to/backup/database_dump.sql` with the same result. The longer into the file I got, the longer it was taking for MySQL to read the query.

### Solution

So, after drinking lots of coffee, I came up with an idea. Why not split up the database sql dump into multiple files. So I used the linux `split` command like this:

```bash
cd /path/to/backup/
mkdir splits
split -n 200 database_backup.sql splits/sql_
```

This produced several dozen files in order, and it took about 10 minutes. The -n option told split to split each file up into 200 lines each. So the files were then named sql_aa, sql_ab, sql_ac all the way to sql_fg. Then, I did the following command using cat to pipe the files to mysql:

```bash
cd splits
cat sql_* | mysql -u root -p database_name
```

The only problem with this method is you don’t see a status report for each query executed, it just runs until you hit an error, displaying the error. If no errors occur, it will just return you to the prompt. So to monitor the progress I would execute a `show processlist;` command on mysql to see how far we were.

the entire database was restored. A few things to note, I didn’t try just using cat on the original file to see if it would read the file differently than the was mysql was trying. But the important thing is I got the database restored in a relatively timely manner.

Hopefully, in the very near future, we will have moved to a new score system that doesn’t have almost a billion rows in it.

P.S. There is other way to do it like using [mydumper](https://github.com/mydumper/mydumper) suggested by my friend and also in [AWS](https://docs.aws.amazon.com/whitepapers/latest/amazon-aurora-mysql-migration-handbook/multi-threaded-migration-using-mydumper-and-myloader.html).