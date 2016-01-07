=========
 Aliased
=========

------
 Info
------

Aluseks on Virtualiseerimiskeskkond_, kus serveri peale on installeeritud Network
Alias'i kasutav apache2, kliendi GUI-st saab brauseriga kahele eri IP-le minnes
kahte eri lehte, mis mõlemad on ühes ja samas serveris. Antud näites kuvab üks
minu ees- ja teine perekonnanime.

.. _Virtualiseerimiskeskkond: virtualiseerimiskeskkond.html

----------------------
 Võrgukonfiguratsioon
----------------------

Varasemast seadistatud võrgukonfiguratsioonile tuleb lisada võrgualias. Selleks
:code:`/etc/network/interfaces` faili lisada.

.. code:: bash

  auto eth1:0
  iface eth1:0 inet static
    address 10.0.0.3
    netmask 255.255.255.0
    gateway 10.0.0.254

Nagu järeldada võib, loob see veel ühe adapteri, IP-aadressiga :code:`10.0.0.3`,
kuna :code:`10.0.0.2` on teatavasti kliendi IP.

------------------------
 Apache konfiguratsioon
------------------------

**Serveris**

VirtualHost'id saab nüüd külge ühendada nii IP-dele :code:`10.0.0.1` kui ka
:code: `10.0.0.3`. Failis :code:`/etc/apache2/sites-available/000-default.conf`.

.. code:: bash

  <VirtualHost 10.0.0.1:80>
    ServerName 10.0.0.1
    DocumentRoot /var/www/ees
  </VirtualHost>
  <VirtualHost 10.0.0.3:80>
    ServerName 10.0.0.3
    DocumentRoot /var/www/pere
  </VirtualHost>

Nii :code:`/var/www/ees`, kui :code:`/var/www/pere` kaustades on vastava sisuga
:code:`index.html` failid.

Veebiserveri restart :code:`service apache2 restart`.

---------
 Tulemus
---------

Eeldusel, et :code:`/var/www/ees`, ja :code:`/var/www/pere` kaustad on veebiserverile
loetavad (testimiseks kõlbab chmod 777), avanevad mõlemal IP-l vastavasisulised
saidid.

.. image:: http://i.imgur.com/VtNVMkJ.png

.. image:: http://i.imgur.com/tpuYT7t.png
