=========
 Ansible
=========

------
 Info
------

Aluseks on Virtualiseerimiskeskkond_, kus serveri peale on installeeritud Ansible,
millega klienti hallata. Kliendil GUI puudub.

.. _Virtualiseerimiskeskkond: virtualiseerimiskeskkond.html

------
 FQDN
------

Ansible server vajab lisaks veel FQDN-i. Selle loomine käib :code:`/etc/hosts` failis,
mille lõppu tuleb lisada alljärgnev rida:

.. code:: bash

  10.0.0.1 ansible.local ansible

**NB! Antud failis on OLULINE, et vahed ei oleks loodud tühikuga, vaid kasutades
TAB klahvi!**

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

Seejärel saab avaliku võtme kopeerida üle võrgu kliendi arvutisse:
:code:`ssh-copy-id -i ~/.ssh/id_rsa.pub root@10.0.0.2`

    **Ansible konfigureerimine**

Ansible-le tuleb lisada kliendid, need lisatakse faili :code:`/etc/ansible/hosts`.
Antud näite puhul tuleb faili lõppu lisada järgmised read.

.. code:: bash

  [test]
  10.0.0.2

Seejärel saab Ansible konfiguratsiooni testida, :code:`ping`-ides äsjaloodud
:code:`test` grupis olevaid IP-aadresse: :code:`ansible test -m ping`.

----------
 Playbook
----------

Anible playbookid on oma olemuselt :code:`.yml` failid, mis defineerivad Ansible
jaoks tegevused ja nende järjekorrad. Siinkohal on loodud playbook-iks fail
:code:`playbook.yml`. Playbooki sisuks on Apache2 veebiserveri installatsioon
ja käivitamine :code:`test` grupile.

.. code:: yaml

  ---
  - hosts: test
    tasks:
      - name: Install apache
        apt: pkg=apache2 state=installed update_cache=true
        notify:
          - start apache2

    handlers:
      - name: start apache2
      service: name=apache2 state=started

**NB! Antud failis on oluline, et kõikideks vahedeks on TÜHIKUD. Kaasaarvatud
ridade algused!**

Playbooki saab käivitada nii: :code:`ansible-playbook playbook.yml`

---------
 Tulemus
---------

Edukas Ansible installatsioon ja playbook peaks käivitamisel andma sellise tulemi.

.. image:: http://i.imgur.com/JORVJTM.png
