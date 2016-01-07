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

.. code:: bash

  d-i netcfg/enable boolean false
  d-i netcfg/choose_interface select auto
  d-i netcfg/get_hostname string ubuntu
  d-i netcfg/get_domain string ubuntu.local
  d-i netcfg/wireless_wep string
  d-i hw-detect/load_firmware boolean true

*Märkus: Kui DHCP serveril on Teie süsteemile anda omapoolne hostname ja domeen, siis seda ta ka teeb ning sel juhul siin määratud sätted ei rakendu.*

**Tavakasutaja loomine**

.. code:: bash

  d-i passwd/user-fullname string Juuser Luuser
  d-i passwd/username string juuser
  d-i passwd/user-password luuser insecure
  d-i passwd/user-password-again luuser insecure
  d-i passwd/auto-login boolean true
  d-i user-setup/allow-password-weak boolean true

*Märkus: Millegipärast see ühe installatsiooni korral ei toiminud :(*
*Märkus 2: NB! Parool tuleb ka siin sisestada kaks korda.*

**Kell ja Ajavööndid**

.. code:: bash

  d-i clock-setup/utc boolean true
  d-i time/zone string Europe/Tallinn
  d-i clock-setup/ntp boolean true
  d-i clock-setup/ntp-server string ntp.example.com

*Märkus: Esimese ja viimase sätte muutmine võib tekitada tõsiseid anomaaliaid süsteemi töös!*

**Partitsioonid**

.. code:: bash

  d-i partman-auto/disk string /dev/sda
  d-i partman-auto/method string regular
  d-i partman-auto/choose_recipe select atomic
  d-i partman/confirm_write_new_label boolean true
  d-i partman-md/confirm boolean true
  d-i partman-partitioning/confirm_write_new_label boolean true
  d-i partman/choose_partition select finish
  d-i partman/confirm boolean true
  d-i partman/confirm_nooverwrite boolean true
  d-i partman/mount_style select uuid

**Süsteemi install, rakenduspaketid**

.. code:: bash

  d-i base-installer/kernel/image string linux-image-486
  d-i apt-setup/services-select multiselect security, updates
  d-i apt-setup/security_host string security.debian.org
  tasksel tasksel/first multiselect standard
  popularity-contest popularity-contest/participate boolean false
  d-i finish-install/reboot_in_progress note
  d-i debian-installer/exit/poweroff boolean true

*Märkus: Popularity Contest on Ubuntu kasutajastatistika kogumisteenus.*

-----------------------
 ISO taasgenereerimine
-----------------------

Alljärgnev käsk genereerib automaatse installi iso loodud jagatud kausta,
failina :code:`autoinstall.iso`.

.. code:: bash

  rando@masin:~$ cd ubuntu_files
  rando@masin:~/ubuntu_files$ mkisofs -D -r -V “$IMAGE_NAME” -cache-inodes -J -l -b isolinux/isolinux.bin -c isolinux/boot.cat -no-emul-boot -boot-load-size 4 -boot-info-table -o /media/sf_share/autoinstall.iso .

---------
 Tulemus
---------

Ülalkirjeldatud protsessiga valmib käivitatav :code:`.iso` fail, mille pealt
süsteemi käivitades installitakse automaatselt Ubuntu Desktop OS.
