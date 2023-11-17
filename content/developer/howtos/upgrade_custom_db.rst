:show-content:
:show-toc:

.. _upgrade/upgrade_custom_db:

===============================
Upgrading a customized database
===============================


Upgrading to a new version of Odoo can be challenging, 
especially if the database you work on contains custom modules.  
This page intent is to explain the technical process of upgrading a database with customized modules.
For numerous years, Odoo has followed this process, reaping the rewards of continual improvements along the way.

For a standard overview, please refer to the
:doc:`upgrade documentation </administration/upgrade>`.

We consider a custom module, any module that extends the standard code of Odoo and that was not built with studio. 
Before upgrading those modules, or before asking for them to be upgraded, 
you might want to have a look at the :ref:`Service-level Agreement <upgrade/sla>` to make sure who's responsible for it.

While you work on what we refer to as the **custom upgrade** of your database, 
you must keep in mind what the goals of an upgrade are:

#. Stay supported
#. Get the latest features
#. Enjoy the performance improvement
#. Reduce the technical debt
#. Benefit from security improvements


With every new version of Odoo comes a bunch of changes,
it can be addition but sometimes it also contains refactorization.
All those changes can impact modules on which you have previously developed customization 
which is why you will need to adapt them.
This is why upgrading a database that contains custom modules requires additional steps compared to 
a standard upgrade of an Odoo database since the source code of the custom modules
must be upgraded as well. 

Here are the steps we follow at Odoo to upgrade such databases:

#. :ref:`Stop the devolopments and challenge them <upgrade/stop_developments>`  .
#. :ref:`Request an upgraded database <upgrade/request_upgrade>`.
#. :ref:`Make your module installable on an empty database <upgrade/empty_database>`.
#. :ref:`Make your module installable on the upgraded database <upgrade/upgraded_database>`.
#. :ref:`Test extensively and do a rehearsal <upgrade/testing_rehearsal>`.
#. :ref:`Upgrade in production <upgrade/production>`.


.. _upgrade/stop_developments:

Stop the developments
=============================

Upgrade can be a tough process, starting it requires commitment and will block a significant amount of your development resources.
If you decide to keep going for developments during that process,
you will inevitably have to block resources for a longer time to work on the upgrade.
Those features will need to be re-upgraded and tested, everytime you change them.

This is why we recommend a complete freeze of the codebase when starting the upgrade process.
Of course, you can still work on maintenance but developing new features is not productive.


Once you have stopped development, it is a good practice to assess the developments made
and compare them with the features introduced between you current version and the version you are targeting.
Challenge the developments as much as possible, find functionnal workarounds.
Removing redundancy between your developments and the standard version of Odoo will
lead to an eased upgrade process and reduce your technical debt.


.. note::
   You can find information on the changes between versions in the `release notes
   <https:/odoo.com/page/release-notes>`_. 
   Those may seem light at first but 
   they will help you point out what needs to be checked during the upgrade process.


.. _upgrade/request_upgrade:

Request an upgraded database
======================================

.. During that process, you can also :ref:`Request a test upgraded database
.. <upgrade/request-test-database>` to ensure the request can be successfully processed

TODO short explanation + link to upgrade doc


.. _upgrade/empty_database:

Empty database
===================================

TODO structurize empty database

.. #. list of things to be adapted
.. #. go in details on some of them

.. Then, make your custom modules installable on a new, empty database to ensure dependencies are
.. still correct, fields definitions are still valid, etc. This also require some :ref:`testing
.. <upgrade/test_your_db>` to ensure that all the features of your modules are still working properly.


.. Installing custom modules on an empty database allows you to detect any discrepancies between the
.. source code of your modules and the new version of Odoo, such as missing dependencies in the
.. manifest, broken fields relations, views containing deprecated fields, etc.

.. TODO add why doing this on an empty DB

..    TODO rewrite example (needs to be simpler)
..    In Odoo 12 and before, the `account.invoice` model had a field named `refund_invoice_id` (`source
..    code <https://github.com/odoo/odoo/blob/f7431b180834a73fe8d3aed290c275cc6f8dfa31/addons/account/models/account_invoice.py#L273>`_),
..    which is absent on the `account.move` model after Odoo 13. This field was renamed to
..    `reversed_entry_id` during the upgrade process. It is possible to find this information by
..    searching for another Many2one field in `account.move` related to `account.move`, for example,
..    `in Odoo 16 <https://github.com/odoo/odoo/blob/a0c1e2aa602ae46598a350ea6ae8d8b4a0c1c823/addons/account/models/account_move.py#L453>`_.


.. TODO rephrase more rarely, models can also be renamed or merged into another model. In this case, if a custom
.. model inherits from the renamed or merged model, its inherit attributes must be updated to match the
.. new model name.

..    - Between Odoo 12 and 13, the `account.invoice` model was merged into `account.move`.
..    - Between Odoo 15 and 16, the `sale.subscription` model was merged into `sale.order`.
..    - Between Odoo 15 and 16, the `account.analytic.group` model was renamed to `account.analytic.plan`.

.. If a custom model overrides standard methods, you must ensure that their name still matches the
.. name of the method they are overriding. In case of changes, you can search the method's source code
.. in the new version to find its new name. If the method has been refactored, the source code might
.. not exactly match, and a manual search is then required. The same goes for function calls to those methods.

..    The `sale.subscription` model has a `_prepare_invoice_data` method `in Odoo 15
..    <https://github.com/odoo/enterprise/blob/e07fd8650246d52c7289194dbe2b15b22c6b65e0/partner_commission/models/sale_subscription.py#L86-L92>`_
..    that has been moved and renamed to `_prepare_invoice` in the `sale.order` model `of Odoo 16
..    <https://github.com/odoo/enterprise/blob/b4182d863a3b925dc3fe082484c27dbb1f2a57d8/partner_commission/models/sale_order.py#L62-L68>`_.

.. TODO rephrase mention attrs removal

.. Custom views are usually also impacted with the upgrade, as they may refer fields, models, or
.. other standard views that have been renamed or refactored. They should be adapted to the new
.. version of Odoo to avoid errors when loading them.

.. TODO once done


.. _upgrade/upgraded_database:

Upgraded database
======================================

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
==========================================


.. After this step, it is crucial to do another :ref:`round of testing <upgrade/test_your_db>` to
.. assess your database usability, as well as to detect any issue with the migrated data.

TODO reminders of testing

TODO content rehearsal

.. _upgrade/production:
Production upgrade
==========================================



TODO content
.. Once you are confident that upgrading your database will not cause any issue, you can proceed with
.. the upgrade of your production database by following the process described on the
.. :doc:`/administration/upgrade` page.