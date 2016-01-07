==========
 Fail2Ban
==========

------
 Info
------

Aluseks on Owncloud_, 8.2.1 kuhu serverile installin veel Fail2Ban-i. Fail2Ban ban-ib
peale kolme ebaõnnestunud login katset kas SSH-sse, või owncloudi, IP vastavalt
teenusele kümneks minutiks ära, ning saadab e-maili.

.. _Owncloud: owncloud.html

----------
 Fail2Ban
----------

**Serveris**

Installin Fail2Ban-i.

.. code:: bash

  apt-get install fail2ban

-----
 SSH
-----

**Konfigureerimine**

Fail2Ban vaikekonfiguratsioonifaili muuta ei tohiks, seega kopeerime selle
failiks, mida muuta saame: :code:`cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local`.

Faili avades, võib selle täielikult tühjendada. SSH ban-i saab konfigureerida väga
minimaalse :code:`jail.local` failiga.

.. code:: bash

  [DEFAULT]

  bantime  = 600
  findtime = 600

  [ssh-iptables]

  enabled  = true
  filter   = sshd
  action   = iptables[name=SSH, port=ssh, protocol=tcp]
             sendmail-whois[name=SSH, dest=demo@example.com]
  logpath  = /var/log/auth.log
  maxretry = 3

Selliselt konfigureeritud :code:`jail.local` fail ban-ib pärast kolmandat valet
sisselogimiskatset SSH-ga IP aadressi 10-ks minutiks ära.

Peale konfigureerimise lõpetamist tuleb fail2ban taaskäivitada
:code:`service fail2ban restart`.

**Kontroll**

Kliendi arvutist :code:`ssh kasutajaKedaPole@10.0.0.1`. Mingi suvalise parooli
sisestamisel 3 korda, peaks edasi timeout tulema.

Serveris saab vaadata, kas kirje ka lisati: :code:`iptables -L`. Kui seal on IP
näha, on kõik OK.

----------
 Owncloud
----------

Siin läheb asi tiba keerulisemaks. Luua tuleb fail2ban filter, failina
:code:`/etc/fail2ban/filter.d/owncloud.conf`. Faili sisuks järgnev.

.. code:: bash

  [Definition]
  failregex={"reqId":".*","remoteAddr":".*","app":"core","message":"Login failed: '.*' \(Remote IP: '<HOST>'\)","level":2,"time":".*"}

  ignoreregex =

Jail.local faili tuleb lisada ka owncloudi jail. See võiks välja näha järgmine.

.. code:: bash

  [owncloud]
  enabled = true
  filter  = owncloud
  port    = http
  maxretry = 3
  logpath = /var/owncloud/owncloud.log

Peale konfigureerimise lõpetamist tuleb fail2ban taaskäivitada
:code:`service fail2ban restart`.

**Kontrollimine**

Kliendi arvutist 3 korda aadressil :code:`http://10.0.0.1/owncloud` valesti sisse
logides peaks leht 10-ks minutiks andma andma "Unable to connect" errorit.

------
 Mail
------

Lisame siia külge ka e-mailide saatmise. :code:`jail.local` failis asendame selle
alguse, kuni :code:`[ssh-iptables]`-ini, alljärgnevaga.

.. code:: bash

  [DEFAULT]

  bantime  = 600
  findtime = 600
  destemail = meil@provider.domeen

  banaction = iptables-multiport
  mta = mai
  protocol = tcp
  action_ = %(banaction)s[name=%(__name__)s, port="%(port)s", protocol="%(protocol)s]
  action_mw = %(banaction)s[name=%(__name__)s, port="%(port)s", protocol="%(protocol)s]
              %(mta)s-whois[name=%(__name__)s, dest="%(destemail)s", protocol="%(protocol)s]
  action_mwl = %(banaction)s[name=%(__name__)s, port="%(port)s", protocol="%(protocol)s]
               %(mta)s-whois-lines[name=%(__name__)s, dest="%(destemail)s", logpath=%(logpath)s]
  action = %(action_mw)s #Siin saad muuta by default actionit

*Märkus: action_mw ja action_mwl vajaksid ka whois package't.*
*Märkus 2: action_mw ban-ib ja saadab meili, action_mwl paneb meili juurde ka logiread*

E-maili, kuhu saadetakse saab muuta :code:`destemail` väärtusest.

Peale konfigureerimise lõpetamist tuleb fail2ban taaskäivitada
:code:`service fail2ban restart`.

---------
 Tulemus
---------

Korrektse seadistuse korral järgneval ban-il blokeeritakse nii IP- aadress, kui
saadetakse ka järgneva sisuga e-mail. Ban kestab 10 minutit.

.. image:: http://i.imgur.com/Tpj2Beg.png
.. image:: http://i.imgur.com/3EO6dq4.png
