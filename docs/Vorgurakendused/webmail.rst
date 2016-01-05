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

.. code-block:: bash
  :lineno-start:19

  post: 143
