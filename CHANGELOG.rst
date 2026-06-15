=================================================
foundata.dovecot Ansible collection Release Notes
=================================================

.. contents:: Topics

v1.0.1
======

Release Summary
---------------

Release Date: 2026-06-15

Bugfix release.

Minor Changes
-------------

- ``mailserver_ldap_maildir.yml`` example playbook - Many more deployment-specific values are now overridable ``dovecot_playbook_*`` variables (with the previous values as defaults) instead of being hard-coded: the quota-warning ``From`` address and subject, the quota storage grace, the per-mail size limit and the over-quota / exceeded messages, the quota-warning script path (a single knob shared by the ``service quota-warning`` executable and the rendered script file), the TLS minimum protocol and cipher preference, the IMAP / IMAPS / ManageSieve listener ports, and the Postfix-facing socket directory, user and group.

Bugfixes
--------

- ``mailserver_ldap_maildir.yml`` example playbook - The shipped example no longer carries a placeholder ``CHANGE-ME`` master-user password hash. It is now the required, no-default variable ``dovecot_playbook_master_admin_password_hash`` (validated in ``pre_tasks`` and intended to be supplied via Ansible Vault).

v1.0.0
======

Release Summary
---------------

Release Date: 2026-06-15

First public release, providing all functionality and files.
