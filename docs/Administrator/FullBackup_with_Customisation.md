# FullBackup with Customisation for Migration

## Overview Full backup command

 On the source server (the one to backup), from the frappe-bench directory run:

 ```shell
 bench --site [sitename] destroy-all-sessions
 bench --site [sitename] set-maintenance-mode on
 mysqldump -u root -p[yourpassword] 311f4e070af3fb93 | gzip > /home/def_user/"$(date '+%m%d-%H%M').sql.gz"
 tar -czf /home/def_user/pri-files.tar.gz -C /home/def_user/frappe-bench/sites/site1.local/private/files .
 tar -czf /home/def_user/pub-files.tar.gz -C /home/def_user/frappe-bench/sites/site1.local/public/files .
 scp /home/def_user/*.tar.gz def_user@192.168.222.111:/home/def_user/
 bench --site[sitename] set-maintenance-mode off #optional
 ```

## Detaill Step by Step

 Ok, so I know this looks complicated, but it is really the sure-fire method for getting even the largest and most difficult databases moved without and bench errors. It also includes all of the associated files that are required to support the database you are moving.
 The files are located in the sites /public/files and private/files directories. Without them you will encounter all kinds of errors when you actually try to use your database on the restored server. Here is a brief explanation of each step…

### step 1: force the logout of all users

```shell
bench --site [sitename] destroy-all-sessions
```

This command will force the logout of any users that may be in the system at the time you want to run the backup.

### step2: Turn on maintanance mode

```shell
bench --site [sitename] set-maintenance-mode on
```

This command will prevent anyone from logging back into the site while you are making your clean copy of the database and files. You will have to set it to ‘off’ after you are done.

### step 3: Create Database zip file

 ```shell
 mysqldump -u root -p[yourpassword] 311f4e070af3fb93 | gzip > /home/def_user/"$(date '+%m%d-%H%M').sql.gz"
 
  # <option flag -p > with no space follow by mysql root password 
 ```

 This command is the generic mysql command for backing up a database from the server command line. Notice the following important items in this command:

- The database password follows immediately after the ‘-p’ and there is no space
- the cryptic looking string after the password is the actual database name. You can find it listed in the `site_config.json` file of your site.
- The ‘def_user’ is whatever username on your server you installed the frappe-bench directory (in the default systems it is usually /home/frappe)
- The gzip end of the command calls out a destination file named with the current date as part of the name to make it easier to identify later. The file is deposited in the ‘def_user’ directory so it is easy to locate when you are ready to transfer it to the other server to be restored.

### step4: Create compressed backups of the associated files

 ```shell
 tar -czf /home/def_user/pri-files.tar.gz -C /home/def_user/frappe-bench/sites/site1.local/private/files .
 tar -czf /home/def_user/pub-files.tar.gz -C /home/def_user/frappe-bench/sites/site1.local/public/files .
 ```

 These two ‘tar’ commands make compressed backups of the associated files in the private/files and the /public/files and deposits the files in the def_user directory so it is easy to find. Notice the  space and period at the end of the lines. Those are actually important so don’t leave them out of the command.

### step5: transfers the backup files to target server

```shell
scp /home/def_user/*.tar.gz def_user@192.168.222.111:/home/def_user/
```

This command actually transfers the backup files you made securely to the target server to be restored when you are ready. The first part fo the command tells 'scp where to find the files to be transferred, and the second half identifies the target server by ip address and what user account to deposit the files on the target server. When you run this command you will be asked for the password of the target server account in order to allow the transfer.

### Step6: turns of the maintenance mode (Optional)

```shell
bench --site[sitename] set-maintenance-mode off
```

This command turns of the maintenance mode that was used to prevent logins while you made the backups. If this server is not to be used anymore, then you can skip this step and keep people from using the server until you decide you want it back in service.

------

## Restore Database in Target server

On the TARGET server runf the following commands from the def_user directory:

```shell 
gunzip [yourdatabase.sql.gz]
tar -xzf /home/def_user/local_restore/pri-files.tar.gz -C /home/def_user/frappe-bench/sites/site1.local/private/files/
tar -xzf /home/def_user/local_restore/pub-files.tar.gz -C /home/def_user/frappe-bench/sites/site1.local/public/files

cd frapp-bench
sudo bench --force --site [sitename] restore /home/def_user/[yourdabase.sql]
sudo service mysql restart
```

### Short explaination

Again, I know it seems like a lot of work, but it is rock solid and works well.

The first command unzips the compressed database into it’s native sql format so it can be restored.
the two tar commands directly decompresses the associated files from the tar file into the correct directories to support the database you are going to restore. (There is no space and period at the end of these commands like there was in the compression step).
the ‘cd’ command changes to the frappe-bench bench directory in order to begin the restore process.
the restore command begins with ‘sudo’ (sudo is handled differently in bench) and is the safest way to restore your database (for now). Even though doing it without the sudo at the beginning may work well 98% of the time, you never want to be that 2% of having to start over, so I always use it.
Well, this turned out to be much longer in getting to your goal of a backup and restore operation across servers, but I believe in the long run it is better to have explicit directions than to venture out to do it with incomplete information. If you miss something while trying to figure it out on your own, you might have to start over again with a fresh server to get it right. That is not a good use of your time, so consider doing this way first.

Hope this helps. My directions were based on experience with Debian and Ubuntu servers. You may have to adjust things for Centos or other distro’s.

And as always… Your mileage may vary!

------

### Ref

[Discuss:FullBackup ](https://discuss.erpnext.com/t/full-backup-with-customisation-for-system-migration/50332/3?u=bkm)