# ManageIQ database configuration

ManageIQ uses PostgreSQL Database. So while dealing with ManageIQ database we'll need to follow PostgreSQL commands and procedure to take a database dump as a backup and to restore it in another instance.


|Note:|ManageIQ uses "`vmdb_production`" database |
|----|:----|

---
This is list of steps followed to obtain a dump from a db then retsore it

### Dump the Postgres DB:

1. stop the appliance(s):
    
        #systemctl stop evmserverd
 
    This will stop the evmserverd.service from using database in background but no need to stop them, for creating backup the doesn't affects anywhere.

2. dump the database into file:

    
        pg_dump -Fc vmdb_production > production.dump

3. Copy the dump file to the other ManageIQ instance where the database have to restore.

    We can use `scp` for that as follows:
        
        scp production.dump root@ip_of_other_instance:/root/

### Import the Postgres dump

1. Take `production.dump` to the local machine.

2. Stop the backend processes

        # systemctl stop evmserverd
3. Drop existing database `vmdb_production` 

        # dropdb vmdb_production

4. create new database named `vmdb_production`

        # createdb vmdb_production

5. restore the database from dump file

        # pg_restore -d vmdb_production "/path/to/production.dump
6. change directory to `/var/www/miq/vmdb/tools` 

        # vmdb  #executing "vmdb" will take you to "/var/www/miq/vmdb/"
        # cd tools
        # bundle exec fix_auth.rb --v2 --invalid bogus
7. Start evm service

        # systemctl start evmserverd
This will do the task of restoring database manually.

In next the same task is automated using Ansible