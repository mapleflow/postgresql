# Postgresql Partition By pg_pathman

we will partition a master table to some table.
	
# Environment

	Debian GNU/Linux 8.7
	PostgreSQL 9.6.2
	
# Installation guide

Your PostgreSQL version must be 9.5+, too old version do not support.

Notice: install some software like `postgresql-common`, `postgresql-client` etc may empty your db or config, so you must backup or care db before install, remove, reinstall.

check before you install `sudo dpkg --list | grep postgre`
		
    libpq-dev, libraries and headers for C language frontend development
    libkrb5-dev, Headers and development libraries for MIT Kerberos     
    pgadmin3, pgAdmin III graphical administration utility
    postgresql-client-X.Y, client libraries and client binaries
    postgresql-X.Y, core database server
    postgresql-contrib-9.4, additional supplied modules
    postgresql-server-dev-X.Y, libraries and headers for C language backend development
	postgresql, object-relational SQL database
	postgresql-all, metapackage depending on all PostgreSQL server packages
	postgresql-client, front-end programs for PostgreSQL
	postgresql-client-common, manager for multiple PostgreSQL client versions
	postgresql-common, PostgreSQL database-cluster manager
	postgresql-contrib, additional facilities for PostgreSQL
	postgresql-doc, documentation for the PostgreSQL database management system
	postgresql-server-dev-all, extension build tool for multiple PostgreSQL versions 

## postgresql		

	# some lib you may need for 9.6
	sudo apt-get install postgresql-server-dev-9.6 libkrb5-dev libpq-dev	
	sudo apt-get --fix-broken install
	
In some system, multi-version are confused, it will make trouble for you.

	sudo apt-get remove --purge postgresql-X.Y
	
download suitable version for your system in https://www.postgresql.org/download/	

## pg_pathman

download and buid it, your may failed. see toubleshoot for solution.

	git clone https://github.com/postgrespro/pg_pathman.git
	sudo make install USE_PGXS=1

find `postgresql.conf`, append extension `pg_pathman` save.

	sudo vi /etc/postgresql/9.6/main/postgresql.conf
	shared_preload_libraries = 'pg_pathman'
	
in sometime your may need `pg_stat_statement`, it is a very userful module,
see detail https://www.postgresql.org/docs/current/static/pgstatstatements.html
    
    # notice: you must put pg_stat_statements after pg_pathman
    shared_preload_libraries = 'pg_pathman,pg_stat_statements' 
    
restart your `postgresql` service after, you can also view status

    ➜  ~ sudo systemctl restart postgresql                      
	➜  ~ sudo systemctl status postgresql
	● postgresql.service - PostgreSQL RDBMS
	Feb 25 16:23:54 debian systemd[1]: Starting PostgreSQL RDBMS...
	Feb 25 16:23:54 debian systemd[1]: Started PostgreSQL RDBMS.
    
try `createdb` or use a exists db for test or develop, the extension is just for your config db, not for all your db. The extension must be installed and apeend in `postgresql.conf` and preloaded by postgresql rightly.

we create db named `yourdb`, create extension for it.
    
    ➜  ~ sudo su postgres
    postgres@debian:/$ createdb yourdb;
	postgres@debian:/$ psql yourdb;
	psql (9.6.2)
	Type "help" for help.
	yourdb=# create extension pg_pathman;
	CREATE EXTENSION
	

	yourdb=# \dx
	                   List of installed extensions
	    Name    | Version |   Schema   |         Description          
	------------+---------+------------+------------------------------
	 pg_pathman | 1.3     | public     | Partitioning tool
	 plpgsql    | 1.0     | pg_catalog | PL/pgSQL procedural language
	(2 rows)	
	
postgresql useful modules： https://www.postgresql.org/docs/current/static/contrib.html. 

	yourdb=# create extension pg_stat_statements;
	CREATE EXTENSION
	yourdb=# \dx
	                                     List of installed extensions
	        Name        | Version |   Schema   |                        Description                        
	--------------------+---------+------------+-----------------------------------------------------------
	 pg_pathman         | 1.3     | public     | Partitioning tool
	 pg_stat_statements | 1.4     | public     | track execution statistics of all SQL statements executed
	 plpgsql            | 1.0     | pg_catalog | PL/pgSQL procedural language
	(3 rows)

## Toubleshoot

### when you use `sudo make install USE_PGXS=1`

	1./usr/lib/postgresql/9.6/lib/pgxs/src/makefiles/pgxs.mk: No such file or directory

or 
	
	2.You need to install postgresql-server-dev-X.Y for building a server-side extension or libpq-dev for building a client-side application.

use `postgresql-server-dev-X.Y` can solve both problem

	sudo apt-get install postgresql-server-dev-X.Y(X, Y depend your version)
	sudo apt-get install postgresql-server-dev-9.6	
	sudo apt-get install libpq-dev
	
fatal error: gssapi/gssapi.h: No such file or directory	

	sudo apt-get install libkrb5-dev
