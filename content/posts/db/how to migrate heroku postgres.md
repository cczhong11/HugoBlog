---
title: "How to migrate your PostgreSQL database out of heroku"
date: "2022-10-29"
description: "step by step to migrate your database out of heroku"
categories:
    - "db"
    - "postgres"
---

# How to migrate your database out of heroku

## background

As we know, heroku will stop supporting the free tier for postgresql and web server. It did not provide good tutorials to help you upgrade your database or migrate your database. So It took me a long time to learn how to migrate my database.

## decision 

Since I did not find a good way to pay the heroku so I dicided to host my web application in my own AWS server. 

## heroku export

This step is pretty simple. Go to your postgresql instance in the heroku and go to durability page. We can see the step to do manual backup. Create one and download to local disk.

![](https://tczimg.s3.amazonaws.com/vscode/7a2b6f306818425591ca663553b0660d.png)

## install on ubuntu

Next, go to your ubuntu server and install postgresql. We need to install the latest version since the heroku using the latest one. We need to have the same version to restore the DB.

```
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" >> /etc/apt/sources.list.d/pgdg.list'
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
sudo apt-get update
sudo apt-get install -y postgresql-client-11
```

## create new db and user with password

Then we need to create new database and user with password. Follow the [link](https://www.digitalocean.com/community/tutorials/how-to-install-postgresql-on-ubuntu-20-04-quickstart).

```

sudo -u postgres createuser <name> --pwprompt
sudo -i -u postgres
createdb db

```

Why we need to create a password for the new user? because when we do the restore the database, it would ask password even we created an user without password. 

## restore the export on server

Run the command and we can see the restored database in the local environment.

```
pg_restore --verbose --clean --no-acl --no-owner -h localhost -U myuser -d mydb latest.dump
```


## connect the new local db

```
sudo -i -u postgres
psql

\c db
select * from table_name;
```
