======
 Mail
======

------
 Info
------

Aluseks on Virtualiseerimiskeskkond_, kus serveri peale on installeeritud meiliserver
koos webmailiga, kuhu kliendile installeritud GUI-st saab browseriga ligi. Lisaks
peab server olema võimaline maili saatma ka välisvõrku.

.. _Virtualiseerimiskeskkond: virtualiseerimiskeskkond.html

-------------
 Meiliserver
-------------

**Serveris**

    **Mailname**
Alustuseks tuleb ära muuta süsteemi mailname. Failis :code:`/etc/mailname`
muuta seal olev väärtus :code:`mail`-iks.

    **Meiliserver**

Meiliserverina kasutusel postifx. Install :code:`apt-get install postfix`. Installimise
ajal konfguratsioonisäteteks valida :code:`No configuration`, niiviisi on hiljem
võimalik konfigureerida rohkem seadeid.

    **Meiliserveri konf**

Ümberkonfigureerimise alustamiseks :code:`dpkg-reconfigure postfix`. Järgnevatel
ekraanidel võiks teha sellised valikud, loomaks meiliserverit :code:`fun.loc`.

.. code:: bash

  OK
  Internet Site
  Mail Name: fun.loc
  Root & Postmaster recipient: rando
  Destinations to accept for mail: fun.loc fun.fun.loc localhost.loc localhost
  Force Synchronous Updates: Yes
  Local Networks: 127.0.0.0/8 10.0.0.0/24
  Use procmail: Yes
  Size limit:
  Local address Extension char: +
  Internet protocols to use: ipv4


  **meiliserveri testimine**

Saadan meili esmalt välisele meilikontole.

.. code:: bash

  mail meil@gmail.com
  Subject: Test
  Lorem Ipsum dolor sit amet
  .#enter
  cc: #enter


**NB! #enter tähistab ENTER klahvi füüsilist vajutamist!**

Kui e-mail jõudis kohale, on süsteem õigesti konfigureeritud.

---------
 Dovecot
---------

**Serveris**

Dovecot on IMAP ja POP3 server. Installimine:
:code:`apt-get install dovecot-core dovecot-pop3d dovecot-imapd`.

    **Konfigureerimine**

Failis :code:`/etc/dovecot/conf.d/10-master.conf` teha järgnevad muudatused.

Real 19.

.. code:: bash

  post: 143


Real 40.

.. code:: bash

  port: 110

Real 96 algav bracket välja kommenteerida, tulemus jääb selline.

.. code:: bash

  unix_listener /var/spool/postfix/private/auth {
      mode = 0660
      user = postfix
      group = postfix
  }

--------------
 SquirellMail
--------------

SquirellMail on veebipõhine meiliklient

**Severis**

    **Installeerimine**

.. code:: bash

  apt-get install apache2
  a2dissite 000-default
  a2dissite default-ssl
  apt-get install squirrelmail


  **Konfigureerimine**

Kõigepealt default konf ümber kopeerida
:code:`cp /etc/squirrelmail/apache.conf /etc/apache2/sites-available/squirrelmail.conf`.

Seejärel konfiguratsiooni muuta. Ehk failis :code:`/etc/apache2/sites-available/squirrelmail.conf`

Rida 1 kustutada.

Read 21-24 eemaldada kommentaarid (eest kustutada # märk), ning lisada IP aadressid.

.. code:: bash

  <VirtualHost 10.0.0.1>
      DocumentRoot /usr/share/squirrelmail
      ServerName 10.0.0.1
  </VirtualHost>

Seejärel võib veebiserveri taaskäivitada: :code:`service apache2 restart`.

---------
 Tulemus
---------

Õigesti seadistatud süsteemi puhul peaks kliendi masinast brauseriga aadressile
:code:`http://10.0.0.1/owncloud` minnes olema näha squirrelmail, ning süsteemi
kasutajaga (nt. root) peaks saama ka sisse logida, ning e-maile saata nii välisvõrku,
kui ka teistele UNIX-i kasutajatele, kellel on serveris konto.

.. image:: http://i.imgur.com/7XO339W.png
.. image:: http://i.imgur.com/zWKPLA0.png
