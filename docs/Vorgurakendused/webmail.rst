=========
 Ansible
=========

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

Alustuseks tuleb ära muuta süsteemi mailname. Failis :code:`/etc/mailname` olev
:code:`debian` muuta seal olev väärtus :code:`mail`-iks.
