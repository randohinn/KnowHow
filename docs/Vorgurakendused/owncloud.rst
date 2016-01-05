==========
 Owncloud
==========

Lisan Owncloud'i repository apt-get'i allikate nimekirja

.. code:: bash

  echo 'deb http://download.opensuse.org/repositories/isv:/ownCloud:/community/Debian_8.0/ /' >> /etc/apt/sources.list.d/owncloud.list


Installin :code:`Release.key` ja uuendan apt-get'i

.. code:: bash

  cd /tmp
  wget http://download.opensuse.org/repositories/isv:ownCloud:community/Debian_8.0/Release.key
  apt-key add - < Release.key
  apt-get update


Installin ownCloud'i

.. code:: bash

  apt-get install owncloud


Installiprotsessi käigus määran MySQL'ile parooli :code:`mysqlpass`.

-------
 MySQL
-------

Login MySQl'i

.. code:: bash

  mysql -u root -p

Loon ownCloudi jaoks andmebaasi ja kasutaja

.. code:: mysql

  CREATE DATABASE owncloud;
  CREATE USER owncloud@localhost IDENTIFIED BY 'ocpass';
  GRANT ALL PRIVILEGES ON owncloud.* TO owncloud@localhost;
  flush privileges;
  quit

------------
 PostgreSQL
------------

Alternatiivne variant MySQL'ile on kasutada PostgreSQL'i.

Alustuseks tuleb ownCloud küll installeerida, kuid MySQL andmebaasi ja kasutajat mitte luua (root kasutaja parooli määramisest siiski mööda ei pääse, aga see selleks), ning mite käivitada veel ka veebiinstallerit. Seejärel installida postgresql ise, PHP PostgreSQL'i laiendus ning veebiserverile restart teha.

.. code:: bash

  apt-get install postgresql
  apt-get install php5-pgsql
  service apache2 restart

Igaks juhuks tuleks üle vaadata ka php konfifail :code:`/etc/php5/conf.d/pgsql.ini` või owncloudiga kaasa tuleva apache puhul :code:`/etc/php5/apache2/conf.d/20-pgsql.ini`. Fail võiks välja näha järgmine:

.. code:: ini

  # configuration for PHP PostgreSQL module
  extension=pdo_pgsql.so
  extension=pgsql.so

  [PostgresSQL]
  pgsql.allow_persistent = On
  pgsql.auto_reset_persistent = Off
  pgsql.max_persistent = -1
  pgsql.max_links = -1
  pgsql.ignore_notice = 0
  pgsql.log_notice = 0


Loon andmebaasi ja kasutaja

.. code:: bash

  psql -hlocalhost -Upostgres

või kui sedasi postgres'ile ligipääs nurjub, siis

.. code:: bash

  sudo -u postgres psql postgres


ning andmebaasi ja kasutaja loomine.

.. code:: mysql

  CREATE USER username WITH PASSWORD 'password';
  CREATE DATABASE owncloud TEMPLATE template0 ENCODING 'UNICODE';
  ALTER DATABASE owncloud OWNER TO username;
  GRANT ALL PRIVILEGES ON DATABASE owncloud TO username;
  \q

-----------------------
 Owncloud'i andmekaust
-----------------------

Loon ownCloud'ile ka üleslaetud failide hoiustamiseks andmekausta.

.. code:: bash

  mkdir /var/owncloud
  chown www-data:www-data /var/owncloud
  chmod 750 /var/owncloud


Nüüd saan kliendi arvuti brauserist installatsiooni jätkata

.. code:: html

  http://10.0.0.1/owncloud

Loon admin kasutaja.
:code:`Advanced Settings` alt muudan ära data kausta ja valin sobiva andmebaasimootori, ning sisestan ab. andmed.

---------
 Tulemus
---------

Owncloud töötab

.. image:: http://i.imgur.com/b2F2Nzk.png
