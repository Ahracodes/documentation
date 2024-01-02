:show-content:
:show-toc:

.. _upgrade/upgrade_custom_db:

===============================
Upgrading a customized database
===============================


Upgrading to a new version of Odoo can be challenging, especially if the database you work on
contains custom modules. This page intent is to explain the technical process of upgrading a
database with customized modules. For numerous years, Odoo has followed this process, reaping the
rewards of continual improvements along the way.

For a standard overview, please refer to the :doc:`upgrade documentation </administration/upgrade>`.

We consider a custom module, any module that extends the standard code of Odoo and that was not
built with the Studio app. 
Before upgrading those modules, or before asking for them to be upgraded, you might want to have a
look at the :ref:`Service-level Agreement <upgrade/sla>` to make sure who's responsible for it.

While you work on what we refer to as the **custom upgrade** of your database, 
you must keep in mind what the goals of an upgrade are:

#. Stay supported
#. Get the latest features
#. Enjoy the performance improvement
#. Reduce the technical debt
#. Benefit from security improvements

With every new version of Odoo comes a bunch of changes, it can be addition but sometimes it also
contains refactorization. All those changes can impact modules on which you have previously
developed customization which is why you will need to adapt them.
This is why upgrading a database that contains custom modules requires additional steps compared to
a standard upgrade of an Odoo database since the source code of the custom modules
must be upgraded as well. 

Here are the steps we follow at Odoo to upgrade such databases:

#. :ref:`Stop the devolopments and challenge them <upgrade/stop_developments>`.
#. :ref:`Request an upgraded database <upgrade/request_upgrade>`.
#. :ref:`Make your module installable on an empty database <upgrade/empty_database>`.
#. :ref:`Make your module installable on the upgraded database <upgrade/upgraded_database>`.
#. :ref:`Test extensively and do a rehearsal <upgrade/testing_rehearsal>`.
#. :ref:`Upgrade in production <upgrade/production>`.


.. _upgrade/stop_developments:

Stop the developments
=====================

Upgrade can be a tough process, starting it requires commitment and will block a significant amount
of your development resources. If you decide to keep going for developments during that process, you
will inevitably have to block resources for a longer time to work on the upgrade.
Those features will need to be re-upgraded and tested, everytime you change them.

This is why we recommend a complete freeze of the codebase when starting the upgrade process.
Of course, you can still work on bugfix but developing new features is not productive.

Once you have stopped development, it is a good practice to assess the developments made and compare
them with the features introduced between you current version and the version you are targeting.
Challenge the developments as much as possible, find functionnal workarounds.
Removing redundancy between your developments and the standard version of Odoo will lead to an eased
upgrade process and reduce your technical debt.


.. note::
   You can find information on the changes between versions in the `release notes
   <https:/odoo.com/page/release-notes>`_. 
   Those may seem light at first but they will help you point out what needs to be checked during
   the upgrade process.


.. _upgrade/request_upgrade:

Request an upgraded database
============================

Once the developments have stopped for the custom modules and the implemented features have been
challenged to remove redundancy and unnecessary code, the next step of the upgrade process is to
request an upgraded test database.

To request the upgraded test database, follow the steps mentioned in 
:ref:`obtaining an upgraded test database <upgrade/request-test-database>`, depending on the hosting
type of your database.

The purpose of this stage is not to start working with the custom modules in the upgraded database,
but to make sure the standard upgrade process works seamlessly, and the test database is delivered
properly. If that's not the case, and the upgrade request fails, request the assistance of Odoo via
the `support page <https://odoo.com/help?stage=migration>`__ by selecting the option related to
testing the upgrade. 


.. _upgrade/empty_database:

[WIP] Empty database
====================

Before working on an upgraded test database, we recommend to make the custom developments work on an
empty database in the targeted version of your upgrade.

This ensures that the customization is compatible with the new version of Odoo, allows to analyse
how does it behave and interact with the new features, and guarantees that they will not cause any
issue when upgrading the database.

Making the custom modules work in an empty database also helps avoiding changes and wrong
configurations that might be present on the production database (like studio customization,
customized website pages, mail templates or translations). They are not intrinsically related to the
custom modules and that can raise unwanted issues in this stage of the upgraded process.

To make custom modules work on an empty database we advise to follow these steps:

  - :ref:`Make them installable <upgrade/empty_database/modules_installable>`
  - :ref:`Test and fixes <upgrade/empty_database/test_fixes>`
  - :ref:`Clean the code <upgrade/empty_database/clean_code>`
  - :ref:`Make standard tests run successfully <upgrade/empty_database/standard_test>`


.. _upgrade/empty_database/modules_installable:

Make custom modules installable
-------------------------------

The first step is to make the custom modules installable in the new Odoo version.
This means, in a first instance, making sure there is no traceback or warnings when installing them.
For this, install the custom modules, one by one, in an empty database of the new Odoo version and
fix the tracebacks and warnings that arise from that.

.. TODO Re-check and explain better the examples, ideally add references to PR such as attrs change

This process will help detect issues during the installation of the modules. For example:

- Invalid module dependencies
- Syntax change: assets declaration, OWL updates, attrs.
- References to standard fields, models not existing anymore or renamed.
- Xpath that moved or were removed.
- Methods renamed or removed .
- ...

.. _upgrade/empty_database/test_fixes:

Test and fixes
--------------

Once there are no more tracebacks when installing the modules, the next step is to test them.
Even if the custom modules are installable on an empty database, this does not warranties there are
no errors during their execution. Because of this, we encourage to test thoroughly all the
customization to make sure everything is working as expected.

This process will help detect further issues that are not identified during the module installation
and can only be detected in runtime. For example, deprecated calls to standard python or OWL
functions, non existing references to standard fields, etc.

We recommend to test all the customization, specially the following elements:

  - Views
  - Mail templates
  - Reports
  - Server actions and automated actions
  - Changes in the standard workflows



.. For further information about testing a database, you can check this page: 
.. :ref:`Testing the new version of the database <upgrade/test_your_db>`.
.. This can also be applied to your custom modules on an empty database


We also encourage to write automated tests to save time during the testing iterations, increase the
test coverage, and ensure that the changes and fixes introduced do not break the existing flows.
If there are tests already implemented in the customization, make sure they are upgraded to the new
Odoo version and run successfully, fixing issues that might be present.

.. _upgrade/empty_database/clean_code:

Clean the code
--------------

At this stage of the upgrade process, we also suggest to clean the code as much as possible.
This includes: 

  - Remove redundant and unnecessary code.
  - Remove features that are now part of Odoo standard, as described in the
    :ref:`Stop the developments <upgrade/stop_developments>` section.
  - Clean commented code if it is not needed anymore.
  - Refactor the code (functions, fields, views, reports, etc.) if needed.

.. _upgrade/empty_database/standard_test:

Standard tests
--------------

Once the previous steps are completed, we advise to make sure all standard tests associated to the
dependencies of the custom module pass.

Standard tests ensure the validation of the code logic but they also prevent data corruption.
They will help you identify bugs or unwanted behavior before you work on your database.

In case there are standard test failing, we suggest to analyze the reason for their failure:

  - The customization changes the standard workflow: Adapt the standard test to your workflow
  - The customization did not take into account a special flow: Adapt your customization to ensure
    it works for all the standard workflows


.. _upgrade/upgraded_database:

Upgraded database
=================

.. Once your modules are installable and working properly (see
.. :ref:`Testing your database <upgrade/test_your_db>`), it is time to make them work on an upgraded
.. database to ensure that they do not depend on a previous installation (e.g., modules already
.. installed, data already present, etc.). During this process, you might have to develop
.. :ref:`migration scripts <upgrade/migration-scripts>` to reflect changes in the source code of
.. your custom modules to their corresponding data.


.. TODO rephrase Reaching this step requires both the source code of your custom modules to be upgraded and a
.. successful :ref:`upgrade request <upgrade/request-test-database>`. If that is the case, you can
.. now test your modules on an upgraded database to ensure that the upgrade did not remove any
.. data, and that your modules are still working properly.

.. TODO migrate your data and migration scripts

.. #. Detail "data to be migrated"

.. When renaming fields in the process of upgrading the source code of your custom modules, the data
.. from the old field must be migrated to the new one. This can be done via a :ref:`migration script
.. <upgrade/migration-scripts>` using the `rename_field` method from the
.. `upgrade-util package <https://github.com/odoo/upgrade-util/blob/220114f217f8643f5c28b681fe1a7e2c21449a03/src/util/fields.py#L336>`__.
.. However, this only renames the field and column names. Therefore, custom views, reports, field
.. relations, automated actions, etc., might still refer to the old field name and need to be
.. updated in the :ref:`migration script <upgrade/migration-scripts>` as well.

.. _upgrade/testing_rehearsal:

Testing and rehearsal
=====================


.. After this step, it is crucial to do another :ref:`round of testing <upgrade/test_your_db>` to
.. assess your database usability, as well as to detect any issue with the migrated data.

TODO reminders of testing

TODO content rehearsal

.. _upgrade/production:

Production upgrade
==================



TODO content
.. Once you are confident that upgrading your database will not cause any issue, you can proceed with
.. the upgrade of your production database by following the process described on the
.. :doc:`/administration/upgrade` page.