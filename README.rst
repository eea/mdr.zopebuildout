===============================================
Zope buildout for https://mdr.eionet.europa.eu/
===============================================

.. contents ::

This buildout will create an isolated environment for running MDR Reportek.
There are three configurations available for running this buildout::
 1. production (production)
 2. testing (staging)
 3. development (devel)


Project name
------------
The project name is MDR: Mediterranean Data Repository and it's based on Zope framework.


Prerequisites - System packages
-------------------------------
These should be installed by the sysadmin (needs root)
This buildout was tested on linux (debian based and RHEL based)
and Max OS X

RHEL based systems
~~~~~~~~~~~~~~~~~~
We will need pip to install some python related packages for versions greater
than the python shipped with RHEL 6.5. We will also need additional repos: PUIAS::

  $ sudo bash
  $ yum install python-pip python27-setuptools subversion git libxml2-devel libxslt-devel munin-node perl-XML-SAX cronie

Reportek-converters system packages::

  $ yum install unrar unzip p7zip mdbtools wv xlhtml libxslt poppler-utils gdal-libs java-1.6.0-openjdk
  $ easy_install-2.7 virtualenv

Make sure crond (from cronie package) starts automatically on RHEL systems.

Debian based systems
~~~~~~~~~~~~~~~~~~~~
::

  $ sudo bash
  $ apt-get install python2.7 python2.7-dev python-ldap python-setuptools subversion git libxml2-dev libxslt-dev munin-node libxml-sax-perl python-virtualenv

Reportek-converters system packages::

  $ apt-get install unrar p7zip-full mdbtools wv xlhtml xsltproc unzip ppthtml pdftohtml python-gdal openjdk-6-jre

Product directory
~~~~~~~~~~~~~~~~~
::

  $ mkdir -p /var/local/mdr/production


Internal dependencies
---------------------
This buildout depends on us having the following products

 * Products.Reportek https://svn.eionet.europa.eu/repositories/Zope/trunk/Products.Reportek/
 * XMLRPCMethod https://svn.eionet.europa.eu/repositories/Zope/trunk/XMLRPCMethod/
 * RDFGrabber https://svn.eionet.europa.eu/repositories/Zope/trunk/RDFGrabber/
 * SmallObligations https://svn.eionet.europa.eu/repositories/Zope/trunk/SmallObligations/
 * reportek-converters https://github.com/eea/reportek-converters


Install Products.Reportek
-------------------------
We shall use virtualenv & co for isolated packages::

  $ cd /var/local/mdr/production
  $ git clone https://github.com/eea/mdr.zopebuildout zope
  $ virtualenv prod-venv
  $ . ./prod-venv/bin/activate
  $ pip install -r zope/requirements.txt


Build production
----------------
Note that the production deployment will use Products.Reportek egg from
http://eggshop.eaudeweb.ro/ ::

  $ cd /var/local/mdr/production
  $ . prod-venv/bin/activate
  $ cd zope
  $ curl -O http://downloads.buildout.org/2/bootstrap.py
  $ python bootstrap.py
  $ cp secret.cfg.sample secret.cfg
  $ vim secret.cfg

Edit secret.cfg and change all the passwords. This file should not be added to Git because it is secret :).
Run buildout using the production.cfg configuration ::

  $ ./bin/buildout -c production.cfg
  $ ./bin/supervisorctl reload 1>/dev/null || .bin/supervisord

Check logs/supervisor.log to see if all the procs started


Build staging
-------------
This deployment is what runns behind https://mdr-test.eionet.europa.eu/
Note that staging will user Products.Reportek from sources (through mr.developer)
https://svn.eionet.europa.eu/repositories/Zope/trunk/Products.Reportek/ ::

  $ mkdir -p /var/local/mdr/staging
  $ cd /var/local/mdr/staging
  $ git clone https://github.com/eea/mdr.zopebuildout zope
  $ virtualenv staging-venv
  $ . staging-venv/bin/activate
  $ pip install -r zope/requirements.txt
  $ cd zope
  $ curl -O http://downloads.buildout.org/2/bootstrap.py
  $ python bootstrap.py
  $ cp secret.cfg.sample secret.cfg
  $ vim secret.cfg

Edit secret.cfg and change all the passwords.
Run buildout using the staging.cfg configuration::

  $ ./bin/buildout -c staging.cfg
  $ ./bin/supervisorctl reload 1>/dev/null || .bin/supervisord


Build devel
-------------
Note that devel will user Products.Reportek from sources (through mr.developer)
https://svn.eionet.europa.eu/repositories/Zope/trunk/Products.Reportek/
but has always-checkout = false so that you can control the version of your sources::

  $ mkdir -p /var/local/mdr/devel
  $ cd /var/local/mdr/devel
  $ git clone https://github.com/eea/mdr.zopebuildout zope
  $ virtualenv devel-venv
  $ . devel-venv/bin/activate
  $ pip install -r zope/requirements-dev.txt
  $ cd zope
  $ curl -O http://downloads.buildout.org/2/bootstrap.py
  $ python bootstrap.py
  $ cp secret.cfg.sample secret.cfg
  $ vim secret.cfg

Edit secret.cfg and change all the passwords.
Run buildout using the devel.cfg configuration::

  $ ./bin/buildout -c devel.cfg
  $ ./bin/instance

Find out what dir the reportek.converters egg is intalled to and start gunicorn::
  * $ cd eggs/reportek.converters-<ver>.egg/Products/reportek.converters/ && ../../../../zope/bin/gunicorn -b localhost:5002 web:app


========
Contacts
========
The project owner is Søren Roug (soren.roug at eaa.europa.eu)

Other people involved in this project are::
 - Cornel Nițu (cornel.nitu at eaudeweb.ro)
 - Miruna Bădescu (miruna.badescu at eaudeweb.ro)
 - Daniel Mihai Bărăgan (daniel.baragan at eaudeweb.ro)


=========
Resources
=========


Hardware
--------

Minimum requirements:
 * 1024MB RAM
 * 1 CPU 1.8GHz or faster
 * 5GB hard disk space

Recommended:
 * 4096MB RAM
 * 4 CPU 2.4GHz or faster
 * 8GB hard disk space


Software
--------

Any recent Linux version.
apache2, local ldap server

=====================
Copyright and license
=====================

The Initial Owner of the Original Code is European Environment Agency (EEA). All Rights Reserved.

The MDR Original Code (this buildout and products listed at section 'Internal dependencies') is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; either version 2 of the License, or (at your option) any later version.

More details under `LICENSE.txt`_.

-------

.. _`LICENSE.txt`: https://github.com/eea/mdr.zopebuildout/blob/master/LICENSE.txt
