# Restoring From ERPNext Backup

## Pre-request 

-zip file installation

## Create Backups

using command `bench backup --site [site_name]

### Download backup files

```shell
 cd /tmp
 wget [DATABASE BACKUP FILE URL]
 wget [FILES BACKUP FILE URL] (this URL is to be made based on what is shown on the DATABASE BACKUP FILE URL)
```

Unzip files (this step is not necessary for the latest version)

```shell 
 gunzip [DATABASE BACKUP FILE.sql.gz]
 tar xvf [FILES BACKUP.tar]
```

### Version ~9+ (Restore via Frappe Bench)

To restore a site database you need to first have/setup a working site. Once you have a working site, you can use the following steps to restore your database.

Go the bench folder, which is usually frappe-bench. Run the following bench commands to restore your database and files
Note1: the following command will overwrite existing data.
Note2: If restoring from another machine, the sitename has to exist before you can restore the sql and files.

```shell
bench --site [sitename] --force restore [path to database backup file] --with-private-files [relative-path-to-private-files-backup-file] --with-public-files [relative-path-to-public-files-backup-file]
bench --site [sitename] migrate # to migrate after restore DB

- [sitename] - the name of the working site to which you want to restore your data.
- [path to database backup file] - path to database backup file that you downloaded (this path should be a relative path from the current bench directory). The name of this file usually ends with ...-database.sql.gz
- [relative-path-to-private-files-backup-file] - path to private files backup file that you downloaded (this path should be a relative path from the sites directory). The name of this file usually ends with ...-private-files.tar
- [relative-path-to-public-files-backup-file] - path to public files backup file that you downloaded (this path should be a relative path from the sites directory). The name of this file usually ends with ...-files.tar

### Optional: Restoring only the database

If you prefer to restore only the database, you can do so by running the following bench command.

```shell 
bench --site [sitename] --force restore [path to database backup file]
```

### Restore Files

```shell
cp /tmp/[FILES BACKUP EXTRACTED FOLDER/---/public/files/*] [BENCH]/sites/[SITENAME]/public/files/
```

--------

### Version 8 (Frappe Bench)

Go to the frappe-bench folder
Run the following bench commands (to restore db and run latest)
Note: You can also restore using mysql
Note: If restoring from another machine, the sitename has to exist before you can restore the sql and files

```shell
bench --site [sitename] --force restore /path/to/SQLFILE
bench --site [sitename] migrate
```

 Restore Files

```shell
cp /tmp/[FILES BACKUP EXTRACTED FOLDER/---/public/files/*] [BENCH]/sites/[SITENAME]/public/files/
```

### Version 4 (Frappe Bench)

Go to the frappe-bench folder
Run the following frappe commands (to restore db and run latest)
Note: You can also restore using mysql

```shell
bench frappe --restore DBNAME SQLFILE
bench frappe [SITENAME] --latest
```

Restore Files

```shell
cp /tmp/[FILES BACKUP EXTRACTED FOLDER/---/public/files/*] [BENCH]/sites/[SITENAME]/public/files/
```

### Version 3

Go to your ERPNext installation folder
When restoring from database, the 'Administrator' user password gets reset to 'admin'. To set a better password when restoring, set admin_password variable in conf.py to the desired 'Administrator' user password.
Restore database using:

```shell
lib/wnf.py --install [DATABASE NAME] /tmp/[DATABASE BACKUP FILE.sql]
```

Copy extracted files

```shell
cp /tmp/[FILES BACKUP EXTRACTED FOLDER/---/public/files/*] [YOUR ERPNEXT INSTALLATION]/public/files/
```

### Ref 

[restore](https://decode.giodc.com/erp/erpnext-restore-backup/)
[Restoring From ERPNext Backup Ref:](https://github.com/frappe/erpnext/wiki/Restoring-From-ERPNext-Backup)
[youtube: how to restore backup](https://www.youtube.com/watch?v=3qSnwLJmgmg)
[youtube: how to restore backup2](https://www.youtube.com/watch?v=GbdWF8rohzk&t=1756s)