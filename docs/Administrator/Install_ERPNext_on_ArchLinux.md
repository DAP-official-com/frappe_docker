# Arch Linux installation

## Install pre-requisites

```shell
pacman -Syu
pacman -S mariadb redis python2-pip wkhtmltopdf git npm cronie nginx openssl
npm install -g yarn
```

## Setup MariaDB

```shell
mysql_install_db --user=mysql --basedir=/usr --datadir=/var/lib/mysql
systemctl start mariadb
mysql_secure_installation
```

>For more information - https://wiki.archlinux.org/index.php/MySQL#Installation

Add the following in mysql configuration located in /etc/mysql/my.conf

```shell
  nano /etc/mysql/my.cnf
```
Then edit `my.conf` as following:

  [mysqld]
  innodb-file-format=barracuda
  innodb-file-per-table=1
  innodb-large-prefix=1
  character-set-client-handshake = FALSE
  character-set-server = utf8mb4
  collation-server = utf8mb4_unicode_ci

  [mysql]
  default-character-set = utf8mb4

>Note: Using `Ctrl + X` and then press `Y` to save and qiute the file.

## Start services

To strat the servise

```shell
systemctl start mariadb redis
```

 if you don't have cron service enabled you would have to enable it by command:

```shell
systemctl enable cronie
```

## Install ERPNext

```shell
git clone https://github.com/frappe/bench bench-repo
pip2 install --user -e bench-repo # no root required installing as user.
echo "export PATH=$HOME/.local/bin:$PATH" >> ~/.bashrc # add ~/.local/bin to path if not already there.
source ~/.bashrc
bench init frappe-bench && cd frappe-bench
bench get-app erpnext https://github.com/frappe/erpnext
bench new-site site1.local
bench --site site1.local install-app erpnext
bench use site1.local
bench start
```

Congrats, now open http://localhost:8000 