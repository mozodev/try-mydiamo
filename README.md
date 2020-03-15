# Try [![MyDiamo](https://mydiamo.com/wp-content/uploads/2017/08/mydiamo_logo-op.png)](https://mydiamo.com/)

> MyDiamo is a free opensource database encryption solution,
available to all for non-commercial use.    
The commercial license is available
for enterprises and organizations who desire extended features.    
The First Comprehensive DB Encryption
for MySQL, MariaDB, Percona and PostgreSQL    

## Requirements

- [virtualbox](https://www.virtualbox.org/)
- [vagrant](https://www.vagrantup.com/)


> [How to install vagrant on windows](https://github.com/neikei/install-vagrant-on-windows)    
mysql sample database from [hhorak/mysql-sample-db](https://github.com/hhorak/mysql-sample-db)

## [Supported mysql versions](https://mydiamo.com/downloads/mydiamo-free-license/)

- 5.6.10, 5.6.12, 5.6.15, 5.6.16, 5.6.19, 5.6.20, 5.6.21, 5.6.22, 5.6.23, 5.6.24, 5.6.25, 5.6.27, 5.6.31, 5.6.33, 5.6.35, 5.6.36, 5.6.40
- 5.7.9, 5.7.10, 5.7.16, 5.7.17, 5.7.18
- 8.0.13

## Let's try!

```bash
$ git clone https://github.com/pentasecurity/try-mydiamo
$ cd try-mydiamo
$ vagrant up
...
$ vagrant ssh
# be root!
vagrant@vagrant-ubuntu-trusty-64:~$ sudo su
root@vagrant-ubuntu-trusty-64:~# cd ~ && source .profile

# check sample database
root@vagrant-ubuntu-trusty-64:~# mysql -e 'USE sample; SHOW tables;'
+------------------+
| Tables_in_sample |
+------------------+
| customers        |
+------------------+

# Step 1. Security Agent Installation
root@vagrant-ubuntu-trusty-64:~# ./install_MyDiamo_for_MySQL_v4.0.6.3_linux.run
# mysql root password is 'root'

# generate site.key, console.key, db.key
root@vagrant-ubuntu-trusty-64:~# keygen -a -N TryMyDiamo
# site.key, console.key, db.key => TryMyDiamo1!

# start keymanager service
root@vagrant-ubuntu-trusty-64:~# keymanager_cis-3.2 -start

# Step 2. License Activation
root@vagrant-ubuntu-trusty-64:~# damo_CLI -start

# request Free License at
# https://mydiamo.com/download-mdfl-for-linux/
AuthID: MDFL-xxxxxxxx-2020-03-xx_00-00-00
AuthCode: {64-digits-hash}
```

# Let's encryt table column!

```
# 1. Create Encryption Policy
https://mydiamo.com/guide/operation/encryption/1

MyDiamo > CREATE POLICY phoneNum"AES"FIV"0"0"CBC"RAW

# 2. Assign the column encryption settings
https://mydiamo.com/guide/operation/encryption/2

# MyDiamo > SET COLUMN <OWNER>"<TABLE>"<COLUMN>"<POLICY ID>
MyDiamo > SET COLUMN sample"customers"phone"phoneNum

# 3. Grant Encryption Privileges
https://mydiamo.com/guide/operation/encryption/3

# MyDiamo > SET PRIV ENC <USER><OWNER><TABLE><COLUMN><ENC PRIV><DEC PRIV>
MyDiamo > SET PRIV ENC root"sample"customers"phone"1"1

# 4. Save MyDiamo Setting
https://mydiamo.com/guide/operation/encryption/4
MyDiamo > Save all

# 5. Migrate Table to MyDiamo Storage

# mysql > call mysql.damo_table_migration('<OWNER>','<TABLE>');
mysql > call mysql.damo_table_migration('sample', 'customers');

```