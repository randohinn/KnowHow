=========
 Ansible
=========

------
 Info
------

Aluseks on Virtualiseerimiskeskkond_, kus serveri peale on installeeritud Ansible,
millega klienti hallata. Kliendil GUI puudub.

.. _Virtualiseerimiskeskkond: virtualiseerimiskeskkond

------
 FQDN
------

Ansible server vajab lisaks veel FQDN-i. Selle loomine käib :code:`/etc/hosts` failis,
mille lõppu tuleb lisada alljärgnev rida:

.. code:: bash

  10.0.0.1 ansible.local ansible

**NB! Antud failis on OLULINE, et vahed ei oleks loodud tühikuga, vaid kasutades
:code:`tab` klahvi!**

Seejärel tuleb server taaskäivitada.

---------
 Ansible
---------

**Serveris**

    **Ansible installeerimine.**

.. code:: bash

  apt-get update
  apt-get upgrade
  apt-get install software-properties-common ansible

  **Paroolivaba ligipääsu kliendile loomine.**

:code:`ssh-keygen -t rsa` - antud käsk küsib küll palju informatsiooni,
kuid sellest võib :code:`enter`-iga läbi joosta - millegi sisestamine vajalik
ei ole. Tulemusena tekib vaikeasukohas olev paroolita fail.

    **Võtmete lisamine**

Äsja loodud võtmed tuleb ssh agendile lisada.

.. code:: bash

  ssh-agent bash
  ssh-add ~/.ssh/id_rsa

Seejärel saab avaliku võtme kopeerida üle vürgu kliendi arvutisse:
:code:`ssh-copy-id -i ~/.ssh/id_rsa.pub root@10.0.0.2`

    **Ansible konfigureerimine**
