=========
 Aliased
=========

------
 Info
------

Aluseks on Virtualiseerimiskeskkond_, kus serveri peale on installeeritud Network
Alias'i kasutav apache2, kliendi GUI-st saab brauseriga kehele eri IP-le minnes
kahte eri lehte, mis mõlemad on ühes ja samas serveris.

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
