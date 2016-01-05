===================
 Ubuntu Unattended
===================

------
 Info
------

Tegemist Ubuntu Unattended Installeerimise iso loomise dokumentatsiooniga.
Loodud :code:`Ubuntu 14.04 LTS Desktop` versioonile. Ühilduvus hilisemate versioonidega
ei ole garanteeritud.

----------------
 Eelnevalt vaja
----------------

* Ubuntu Desktopi-ga virtuaalmasin.
* Teadmine, et siinseid käske tuleb jooksutada **ilma** :code:`rando@masin:~$` eesliiteta.

------------------------
 Ligipääs ISO failidele
------------------------

**Windowsis**

Kasutades HowToGeek_i õpetust, tuleb seadistada jagatud *machine* kaust.

.. _HowToGeek: http://www.howtogeek.com/189974/how-to-share-your-computers-files-with-a-virtual-machine/

Peale seadistamist, tuleb kopeerida VM-i seadistamiseks kasutatud iso fail loodud
kausta.

**Ubuntus**

Ubuntus tuleb lisada esmalt kasutaja :code:`vboxsf` gruppi: :code:`rando@masin:~$  sudo usermod -a -G vboxsf rando`.
Seejärel masinale taaskäivitus.

Loodud jagatud kaust on Linuxis kättesaadav asukohas :code:`/media/sf_share`.

    **ISO külgehaakimine**

Iso tuleb eraldada kausta, kus sellega tööd on võimalik teha.

.. code:: bash

  rando@masin:~$  mkdir iso_mount
  rando@masin:~$  sudo mount -o loop /media/sf_share/ubuntu-14.04.3-desktop-i386.iso iso_mount
  rando@masin:~$  mkdir ubuntu_files
  rando@masin:~$  rsync -a iso_mount/ ubuntu_files/
  rando@masin:~$  sudo chmod -R 777 ubuntu_files

-----------------
 Failimuudatused
-----------------

    **isolinux**

Isolinux-i menüü fail tuleb sundida automaatselt käivitama loodavat unattended
installiprotsessi.

.. code:: bash

  rando@masin:~$  nano ubuntu_files/isolinux/txt.cfg

Terve faili sisu kustutada, ja asendada alljärgnevaga.

.. code:: bash

  default auto
  label auto
  menu label ^Automatically Install Ubuntu
  kernel /casper/vmlinuz
  append file=/cdrom/preseed/ubuntu.seed initrd=/casper/initrd.lz automatic-ubiquity quiet

  **langlist**

Ubuntu vaikimisi laeb alla sigapalju keeli. Limiteerime keele vaid inglise keele
peale, kustutades failis :code:`isolinux/langlist` ära kogu sisu, ning asendades selle
lihtsalt :code:`en`-iga.

---------
 Preseed
---------

Viimane muudetav fail on see, mille abil Ubuntu installi eelkonfigureerida annab.
Asub see asukohas :code:`preseed/ubuntu.seed`. Faili võib seal eelnevalt olevast
puhastada. Alljärgnevalt on toodud selle faili uus sisu, antud ülesande järgi.
Lihtsuse huvides on fail jagatud pealkirjade kaupa.

    **Installer ja klaviatuur**

.. code:: bash

  d-i debian-installer/locale string en_US
  d-i console-setup/ask_detect boolean false
  d-i debian-installer/language string en
  d-i debian-installer/country string NL
  d-i debian-installer/locale string en_GB.UTF-8
  d-i console-setup/layoutcode string ee
  d-i keyboard-configuration/layoutcode string ee

**Võrk**
