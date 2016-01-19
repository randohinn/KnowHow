========
 Nagios
========

------
 Info
------

Aluseks on Virtualiseerimiskeskkond_ & Postfix-i osa Mail_ ist, kus serveri peale on installeeritud Nagios ning Apache2&php5(antud dokk installib apache ja php5 automaatselt), nagios seadistatud
monitoorima mingi muu VM-i pingidele vastamist Antud näites seadistatud monitoorima kliendimasinat. Kui antud masin ei vasta 1 minuti jooksul pingimistele, saadetakse
e-kiri. Lisaks pääseb vaid klientmasinast ligi Nagiose liidesele veebikeskkonnas.

.. _Virtualiseerimiskeskkond: virtualiseerimiskeskkond.html
.. _Mail: webmail.html#meiliserver

--------
 Nagios
--------

**Kasutaja ja grupp**

Luua Nagiosele kasutaja ning grupi.

.. code:: bash

   useradd nagios
   groupadd nagcmd
   usermod -a -G nagcmd nagios

**Eeldused**

Kompileerime oma süsteemis uusima nagiosi. Selleks on vaja aga eelnevalt teatud pakette. Installi need.

.. code:: bash

   apt-get update
   apt-get install curl  build-essential libgd2-xpm-dev openssl libssl-dev xinetd apache2 php5 libapache2-mod-php5 apache2-utils unzip

**Nagios Core**

Uusima Nagios Core saamiseks tuleks külastada Nagiosi Allalaadimislehte_, ning kopeerida sealt uusima allalaadimise lingi (Antud dokumentatsiooni
kirjutamise ajal 4.1.1.tar.gz).

.. _Allalaadimislehte: https://www.nagios.org/downloads/nagios-core/thanks/?t=1452485417

Laadida saadud lingilt alla Nagios Core arhiivi.

.. code:: bash

  cd ~
  curl -L -O https://assets.nagios.com/downloads/nagioscore/releases/nagios-4.1.1.tar.gz

Arhiiv tuleb lahti pakkida, ning saadud kausta siseneda.

.. code:: bash

  tar xvf nagios-*.tar.gz
  cd nagios-*

**Nagios Core kompileerimine**

Enne kompileerimise alustamist, peab selle kompileerimisprotsessi tarbeks konfigureerima, söötes
konfiguratsioonile ette eelnevalt loodud kasutaja ja grupi.

.. code:: bash

  ./configure --with-nagios-group=nagios --with-command-group=nagcmd

Ning nüüd võib kompileerida.

.. code:: bash

  make all

Kui pikk kompileerimisprotsess on lõppenud, käivitada järgnevad käsud, installimaks nagiose, selle skriptid ja näidiskonfiguratsioonifailid.

.. code:: bash

   make install
   make install-commandmode
   make install-init
   make install-config
   /usr/bin/install -c -m 644 sample-config/httpd.conf /etc/apache2/sites-available/nagios.conf

Et veebiliidesest oleks võimalik nagiost käsutada, peab kasutaja :code:`www-data` lisama eelnevalt loodud gruppi.

.. code:: bash

   usermod -G nagcmd www-data

-----------------
 Nagiose Pluginad
-----------------

Ka Nagiose pluginad kompileerime süsteemis. Otsida allalaadimislehelt_ uusim link ning sealt arhiiv alla laadida (Kirjutamise ajal 2.1.1.tar.gz).

.. _allalaadimislehelt: http://nagios-plugins.org/download/?C=M;O=D

.. code:: bash

  cd ~
  curl -L -O http://nagios-plugins.org/download/nagios-plugins-2.1.1.tar.gz

Lahti tuleb pakkida ka see arhiiv.

.. code:: bash

  tar xvf nagios-plugins-*.tar.gz
  cd nagios-plugins-*

**Kompileerimine**

Konfigureerida tuleb ka see kompileerimisprotsess.

.. code:: bash

  ./configure --with-nagios-user=nagios --with-nagios-group=nagios --with-openssl

Nüüd võib kompileerida ja installida.

.. code:: bash

  make
  make install

------
 NRPE
------

Viimane asi, mis manuaalselt kompileerida tuleb, on NRPE. Otsida SourceForge_st uusima versiooni arhiivi link (Kirjutamise hetkel 2.15.tar.gz).

.. _SourceForge: http://sourceforge.net/projects/nagios/files/nrpe-2.x/

Alla laadimine ja lahtipakkimine.

.. code:: bash

  cd ~
  curl -L -O http://downloads.sourceforge.net/project/nagios/nrpe-2.x/nrpe-2.15/nrpe-2.15.tar.gz
  tar xvf nrpe-*.tar.gz
  cd nrpe-*

**Kompileerimine**

Kompileerimisprotsessi konfigureerimine.

.. code:: bash

  ./configure --enable-command-args --with-nagios-user=nagios --with-nagios-group=nagios --with-ssl=/usr/bin/openssl --with-ssl-lib=/usr/lib/x86_64-linux-gnu

Kompileerimine ja install

.. code:: bash

  make all
  make install
  make install-xinetd
  make install-daemon-config

**Konfiguratsioon**

Piirame esmalt ligipääsu NRPE-le nii, et ainult nagiose server saaks sellele ligi. Modifitseerida tuleb faili :code:`/etc/xinetd.d/nrpe`

.. code:: bash

  only_from = 127.0.0.1 10.0.0.1

Seejärel tuleb taaskäivitada ligipääsuga tegelev teenus: :code:` service xinetd restart`.

--------------------------
 Nagiose konfigureerimine
--------------------------

Lõpuks saab teostada esialgse nagiose konfigureerimise. Muudatus faili: :code:`/usr/local/nagios/etc/nagios.cfg`. Kommentaar (#) tuleb eemaldada realt :code:`cfg_dir=/usr/local/nagios/etc/servers`

Vastav kaust tuleb ka luua: :code:`mkdir /usr/local/nagios/etc/servers`.

Järgnevalt tuleks konfigureerida nagiose e-maili saatmine. Failis :code:`/usr/local/nagios/etc/objects/contacts.cfg` leida ja muuta e-maili väärtus.

.. code:: bash

  email                           email@provider.domeen

-------------------------
 Apache konfigureerimine
-------------------------

Aktiveerida tuleb paar moodulit.

.. code:: bash

  sudo a2enmod rewrite
  sudo a2enmod cgi

Nagiose veebiliidesel on vaja ligipääsuks ka kasutajat & parooli. Nende loomine käib nii.

.. code:: bash

  htpasswd -c /usr/local/nagios/etc/htpasswd.users nagiosadmin

Nagiose konfiguratsioonifail tuleb link-ida apache hostitavate saitide kausta.

.. code:: bash

  ln -s /etc/apache2/sites-available/nagios.conf /etc/apache2/sites-enabled/

Nüüd on lõpuks võimalik käivitada nii nagios kui apache2.

.. code:: bash

  service nagios start
  service apache2 restart

Laseme nagiosel automaatselt käivituda koos serveriga: :code:`ln -s /etc/init.d/nagios /etc/rcS.d/S99nagios`.

**Veebiliidese ligipääsu piiramine**

Seda võib teha, aga ei pea. Hetkel teeme. Failis :code:`/etc/apache2/sites-available/nagios.conf` tuleb teha järgnevad muudatus.

Need read välja kommenteerida (Lisada #):

.. code:: bash

  Order allow,deny
  Allow from all

Nendelt ridadelt kommentaar eemaldada (# ära). :code:`Allow from` reale lisada oma kliendi privaatne IP.


.. code:: bash

  Order deny,allow
  Deny from all
  Allow from 127.0.0.1 10.0.0.2

Antud muudatused tuleb teha failis kahes kohas!

Teenuste restart

.. code:: bash

  service nagios restart
  service apache2 restart

**Veebiliidesele ligipääs**

Nüüd peaks kliendi masinast, minnes brauseriga aadressile: :code:`http://10.0.0.1/nagios` olema olemas ligipääs nagiose veebiliidesele, seda
eelnevalt loodud kasutajanime ja parooliga. Vasakult Paneelilt **Hosts** valides, peaks näha olema, et hetkel monitoorib nagios vaid iseennast.

----------------------
 Kliendi eelseadistus
----------------------

Kliendiarvutis tuleb installeerida monitoorimiseks nagios plugins ja nrpe server, sedapuhku apt-get'iga.

.. code:: bash

  apt-get update
  apt-get install nagios-plugins nagios-nrpe-server

Lubame ligipääsu meie nagios serverilt failis :code:`/etc/nagios/nrpe.cfg` Reale :code:`allowed_hosts` lisada nagios serveri IP ja reale :code:`server_address` selle masina privaatne IP

.. code:: bash

  allowed_hosts=127.0.0.1,10.0.0.1

NRPE server vajab siinkohal taaskäivitust: :code:`service nagios-nrpe-server restart`

----------------
 Hosti lisamine
----------------

Nagiose serveris tuleb luua konfifail igale masinale, mida monitoorida tahetakse: :code:`touch /usr/local/nagios/etc/servers/HOSTI-NIMI.cfg`. Näiteks :code:`touch /usr/local/nagios/etc/servers/klient.cfg`.

Faili sisu on järgnev.

.. code:: bash

  define host {
	use                             linux-server
    host_name                       klient
    alias                           Kliendimasin
    address                         10.0.0.2
    max_check_attempts              3
    normal_check_interval           1
    retry_check_interval            1
    notification_interval           1
	contact_groups                  admins
  }
  define service {
    use                             generic-service
    host_name                       klient
    service_description             PING
    check_command                   check_ping!100.0,20%!500.0,60%
  }

Monitooringu käivitamiseks :code:`service nagios restart`. Veebiserveris peaks nüüd olema näha uus host.

----------------------------------------
 Apache ja MySQL monitoorimise lisamine
----------------------------------------

Teen ühe Virtualiseerimiskeskkonna_ juurde, kuhu installin LAMP Stack-i. IP 10.0.0.3.

.. _Virtualiseerimiskeskkonna: virtualiseerimiskeskkond.html

**MySQL**

MySQL jookseb vaikimisi pordil 3306, aga ip-l 127.0.0.1. Seda tuleb LAMP serveris muuta, kommenteerides failis :code:`/etc/mysql/my.cnf` välja (# ette) rea :code:`bind-address = 127.0.0.1`.
Lisaks tuleb lubada nagios kasutajal ligipääs igaltpoolt. Teen monitoorimiseks ka testandmebaasi.

.. code:: bash

  mysql -u root –p
  CREATE USER 'nagios'@'localhost' IDENTIFIED BY 'nagios-pass';
  GRANT ALL PRIVILEGES ON *.* TO 'nagios'@'localhost';
  CREATE USER 'nagios'@'%' IDENTIFIED BY 'nagios-pass';
  GRANT ALL PRIVILEGES ON *.* TO 'nagios'@'%';
  FLUSH PRIVILEGES;
  exit;
  
**Nagios teenuste lisamine**

Ei monitoori enam klienti, kirjutan klient.cfg faili üle

Serverifaili sisu võiks olla.

.. code:: bash

  define host {
	use                             linux-server
    host_name                       lambikas
    alias                           Lamp
    address                         10.0.0.3
    max_check_attempts              3
    normal_check_interval           1
    retry_check_interval            1
    notification_interval           1
	contact_groups                  admins

  }
  define service {
    use                             generic-service
    host_name                       lambikas
    service_description             MySQL Connectivity
    check_command                   check_tcp!3306
	contact_groups                  admins

  }
  
  define service {
    use                             generic-service
    host_name                       lambikas
    service_description             Apache server
    check_command                   check_http
	contact_groups                  admins

  }

.. code:: bash

  service nagios restart

  
---------
 Tulemus
---------

Nagios töötab ja saadab e-maile seni, kuni server ise / serveris Apache või MySQL taas püsti on.

.. image:: http://i.imgur.com/hEwi8XE.png

.. image:: http://i.imgur.com/fEh2ovH.png

.. image:: http://i.imgur.com/ulvcl6J.png

.. image:: http://i.imgur.com/2so2YiY.png
