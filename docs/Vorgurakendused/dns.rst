=====
 DNS
=====

------
 Info
------

Aluseks on Virtualiseerimiskeskkond_ kuhu on seadistatud serverile bind9 DNS server. Antud DNS server on seotud kliendi külge, ning pingida on võmalik kasutades domeeninime.

**NB! Klient ei ole graafiline. Graafilises keskkonnas DNS-i külge haakimine antud viisil ei pruugi tomida!**

.. _Virtualiseerimiskeskkond: virtualiseerimiskeskkond.html

-------
 Bind9
-------

Installerimine käib nii - :code:`apt-get install bind9`.

**Hosti lisamine**

Faili :code:`/etc/hosts` tuleb lisada eth1 adapteri IP, koos soovitud domeeninimega. Siinkohal kasutatud domeeninime :code:`jr.local`.
Ehk siis lisada selline rida, **NB! vahekohad TAB-iga**.

.. code:: bash

  10.0.0.1 dns.jr.local    dns

**Resolv.conf**

Eelnevalt vaja installida :code:`resolvconf` package. :code:`apt-get install resolvconf`.

Järgnevalt tuleb seadistada DNS serverile nimeserverid. Faili :code:`/etc/resolvconf/resolv.conf.d/head` sisse alljärgnev.

.. code:: bash

  nameserver    10.0.0.1
  nameserver    8.8.8.8
  nameserver    8.8.4.4
  search    jr.local

Siinkohal tulenevad IP-d :code:`8.8.8.8` ja :code:`8.8.4.4` Google'i avalikest nimeserveritest. Head faili kasutatakse staatiliseks resolv.conf sätete muutmiseks -
vastasel juhul resolv.cofn kirjutataks igal buudil üle.

Keelame võrgult saadava ära. Failis :code:`/etc/dhcp/dhclient.conf` eemaldada request väärtuse tagant :code:`domain-name-servers` ja :code:`domain-search`.


**Bind9 konfigureerimine**

Faili :code:`/etc/bind/named.conf.local` lõppu tuleb lisada järgnevad read.

.. code:: bash

  zone    "jr.local"    {
        type master;
        file "forward";
  };

  zone    "10.in-addr.arpa"    {
        type master;
        file "reverse";
  };

**Tsoonifail**

Kopeerime bind9 algse tsoonifaili, et saaksime seda turvaliselt muuta.

.. code:: bash

  cd /etc/bind/
  cp db.local /var/cache/bind/forward
  cd /var/cache/bind

Teeme muudatused saadud :code:`forward` failis. Tulemus võiks välja näha selline.

.. image:: http://i.imgur.com/Dm6h9oD.png

Kopeerime selle faili uueks failiks nimega :code:`reverse`.

.. code:: bash

  cp forward reverse

Reverse fail mapib IP domeeninime külge. Antud fail peaks pärast muudatusi välja nägema selline.

.. image:: http://i.imgur.com/BYFpRHO.png

**DNS Käima**

Teeme DNS Serverile taaskäivituse, ja kui kõik on korras, peaks asi toimima :code:`/etc/init.d/bind9 restart`.

**Test**

Käsk :code:`nslookup jr.local` peaks tulemuseks tagasi andma ilusti meie IP aadressi.

.. image:: http://i.imgur.com/v9YBIQb.png

-----------------
 Kliendile külge
-----------------

Kliendile on eelnevalt vaja installida :code:`resolvconf` package. :code:`apt-get install resolvconf`.

Kliendi internal network adapterile tuleb faili :code:`/etc/network/interfaces` lisada sellised read ja muuta gateway:

.. code:: bash

    gateway 10.0.0.1
    dns-nameservers 10.0.0.1
    dns-search jr.local
    dns-domain jr-local


Masinale reboot.

**Test**

Nüüd peaks nslookup andma ka kliendist tagasi IP aadressi, ning töötama peaks ka :code:`ping jr.local`.

---------
 Tulemus
---------

Seadistatud on töötav DNS server, mis suudab kliendimasinas ära resolve-ida domeeni :code:`jr.local`.

.. image:: http://i.imgur.com/F71yqwG.png
