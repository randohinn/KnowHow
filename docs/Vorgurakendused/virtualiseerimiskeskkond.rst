==========================
 Virtualiseerimiskeskkond
==========================

------
 Info
------
Võrgurakenduste dokumentatsioon on testitud & koostatud järgneval
virtualiseerimiskeskkonna seadistusel.

Keskkonnas eksisteerib 2 virtuaalmasinat - klient ja server. Mõlemal masinal
on 2 võrguadapterit - nesit esimene pääseb internetti, ning teine on
:code:`internal network` adapter. Mõlemate OS on Debian 8.

----------------------
 Võrgukonfiguratsioon
----------------------

 Serveris
==========

Failis :code:`/etc/network/interfaces` tuleb teha täiendusi. Olemasolev
:code:`allow-hotplug eth0` tuleb muuta :code:`auto eth0`-iks. Vastasel juhul ei
suuda masin hiljem välisvõrku :code:`ping`-ida.

Lisaks tuleb faili lisada ka teine adapter.

.. code::
    auto eth1
    iface eth1 inet static
        address 10.0.0.1
        netmask 255.255.255.0
        gateway 10.0.0.254

 Kliendis
==========


Korrata serverimasinas tehtut. **NB! Kindlasti TULEB Kliendile määrata mingi
muu IP aadress, nt 10.0.0.2 !!**

Võrguadapterite restart mõlemas masinas :code:`/etc/init.d/networking/restart`

----------
 Kontroll
----------


 Serveris
==========


:code:`ping`-ime klienti: :code:`ping 10.0.0.2`

Kui :code:`ping` annab tulemuseks tagasi tulevad paketid, on seadistus korras.


 Kliendis
==========

:code:`ping`-ime serverit: :code:`ping 10.0.0.1`

Kui :code:`ping` annab tulemuseks tagasi tulevad paketid, on seadistus korras.

 Mõlemas
==========

Kuna Kõigi ülesannete tarvis on internetile ligipääsu, siis **peavad** mõlemad
masinad olema suutelised :code:`ping`-ima ka välisvõrku. Jooksutada
:code:`ping google.com`. Kui annab tulemuseks tagasi tulevad paketid, on
kõik korras.

----------
 Hostname
----------

Serveri hostname võiks olla selline, et sellele peale vaadates saab aru,
mis masinaga tegu on. Nt :code:`owncloud` või :code:`ansible`.

Hostname muutmine käib failist :code:`/etc/hostname`, mille sisuks tulebki kirjutada
soovitud hostname. Seejärel tuleb muudatus süsteemis ka rakendada:
:code:`hostname -F /etc/hostname`.

-----
 SSH
-----

Virtualboxi ei saa *copy-paste*'ida, ja see kaaperdab ka hiire ja klaviatuuri.
Lihtsam on kasutada näiteks *Putty*'t. Ligipääsuks tuleb serverile installida
ssh server. :code:`apt-get install openssh-server`. Kuna tavakasutaja õigustega
pole eriti midagi pihta hakata, siis tuleb lubada *root*-ina sisse logimine.

:code:`/etc/ssh/sshd_config` failis rida 28 tuleb muuta selliseks:
:code:`PermitRootLogin yes`.

Seejärel on tarvis ssh server taaskäivitada: :code:`systemctl restart sshd`.

---------
 Tulemus
---------

Selliselt seadistatud virtualiseerimiskeskkond on aluseks siinolevate
Võrgurakenduste dokumentatsioonidele.
