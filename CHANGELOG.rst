=================================================
foundata.dovecot Ansible collection Release Notes
=================================================

.. contents:: Topics

v1.1.0
======

Release Summary
---------------

Release Date: 2026-07-30

Maintenance and bugfix release.

Minor Changes
-------------

- Harden the LDAP Maildir example by requiring encrypted directory connections, validating TLS material, removing local-account fallbacks, and provisioning the mail storage root on fresh hosts.
- The Molecule ``default`` scenario now selects the test backend per platform via a ``type`` key: ``podman`` (container, the default when omitted) or ``libvirt`` (QEMU/KVM virtual machine from a vendor cloud image via a session libvirt daemon, without root privileges). VM platforms allow tests containers cannot cover; commented ``libvirt`` alternates for every platform are included in ``molecule.yml``. ``molecule login`` now works through a per-instance login command for both backends. See ``extensions/molecule/README.md`` for requirements and usage.
- ``run`` role - Configuration changes (``dovecot.conf``, master-users passwd-file) now notify a service reload instead of a restart, so active IMAP sessions survive ordinary reconfiguration. Package installations and upgrades now notify a restart instead (new binaries only take effect with one). The previously unreachable reload handler skips services that are not running yet: on fresh installs the service phase then starts Dovecot with the new configuration.

Security Fixes
--------------

- ``run`` role - Rendered configuration content can no longer leak secrets into task output. In diff mode, the main ``dovecot.conf`` template (which can contain credentials such as an LDAP bind password from ``run_dovecot_passdbs``) printed its full content diff; it now sets ``diff: false`` while keeping ``doveconf`` validation errors visible. The master-users passwd-file task (password hashes) now runs with ``no_log: true``.
- ``run`` role - The rendered quota-warning script (which runs as root) hardened its input handling, notably: The configured ``from``, ``subject`` and ``body`` values are now inserted as shell-quoted literals and printed as data instead of being placed in a here-document. Dovecot's documented ``${PERCENT}`` placeholder is substituted explicitly instead, and line breaks in the ``From`` and ``Subject`` values are folded into spaces to prevent mail header injection.
- ``run`` role - ``run_dovecot_passdbs`` (entries commonly carry inline credentials such as ``static``'s ``password`` or LDAP bind passwords) and ``run_dovecot_external_files.master_users.entries[].password_hash`` are now marked ``no_log`` in the argument specification. Rendering was already protected (``diff: false``), but a failing argument validation and verbose task output could still expose the values.

Bugfixes
--------

- The comment written into neutralized distribution config files contained a stray double quote in the Debian hint (``dpkg -S '<file>'"``), so the suggested command could not be copied and pasted as-is. The quote is removed.
- ``mailserver_ldap_maildir.yml`` example playbook - The quota-warning service now runs as root. With the previous ``user = dovecot`` (as in the upstream examples) the rendered script failed silently on every quota threshold crossing: its ``dovecot-lda`` call has to read the root-only ``dovecot.conf`` (mode ``0600``, may contains credentials) and switch to the mail user, both impossible without root. No warning mail was ever delivered. The ``run_dovecot_external_files`` documentation explains the requirement.
- ``run`` role - External files (master users passwd-file, quota-warning script) at freely configurable paths are now recorded in an ownership manifest (``/etc/dovecot/.ansible-external-files``): renaming a path removes the file at the previous path on the next run, and uninstall cleans up historical paths as well. Before, a renamed master users file (containing password hashes) and an old quota-warning script survived both reconfiguration and uninstall.
- ``run`` role - Mail storage user, group and directory management is no longer silently skipped when ``run_dovecot_mail_user_manage`` is given as a string (e.g. ``"true"`` via ``--extra-vars`` or an INI inventory). The conditions now use the ``bool`` filter instead of the strict ``is true`` identity test, which only accepts native booleans.
- ``run`` role - Platform-specific task files are now guaranteed to run before the shared default tasks. The former single include loop did not preserve that order with several platforms in one play: Ansible batches the includes across hosts and the insertion order depends on when results arrive (non-deterministic), so default tasks could run before platform-specific ones. The includes are now two sequential tasks, which is a hard ordering barrier.
- ``run`` role - The ``master_users`` ``path`` documentation no longer claims the role overrides the default per platform (no such platform override exists and none is planned: a single stable default keeps the path referenced from ``run_dovecot_passdbs`` identical on every platform).
- ``run`` role - The documentation of the ``unmanaged`` service state falsely claimed the service "will not start at boot". The role leaves the service completely alone in this state: both the running state and the boot (enablement) state stay exactly as they are. The description now documents the real behavior.
- ``run`` role - The rendered quota-warning script now delivers the warning mail with ``dovecot-lda -o quota_enforce=no``. The former ``plugin/quota=...:noenforcing`` override is Dovecot 2.3 syntax that 2.4 silently ignores, so the warning mail was rejected exactly when the mailbox was completely full. The ``run_dovecot_settings`` documentation example also no longer shows the removed 2.3 ``plugin { ... }`` block (replaced with the 2.4 ``quota "User quota"`` and ``sieve_script`` equivalents, verified against Dovecot 2.4.1).
- ``run`` role - The rendered quota-warning script now documents the root requirement in its header and fails loudly: it refuses non-root execution with an actionable message (exit 77), rejects malformed arguments with exit 64 and reports failed ``dovecot-lda`` deliveries to the mail log instead of exiting silently.
- ``run`` role - The role now fails fast with an actionable message when ``run_dovecot_settings`` is missing ``dovecot_config_version`` or ``dovecot_storage_version``. Dovecot 2.4 refuses configurations without both pins, so the default empty settings rendered a config that only failed later in the template's ``doveconf`` validation with a less actionable error. The documentation no longer presents ``dovecot_storage_version`` as optional (both pins are mandatory, verified against Dovecot 2.4.1).
- ``run`` role - The service restart and reload handlers were gated only on ``run_dovecot_service_state != 'unmanaged'``. With ``run_dovecot_service_state: "disabled"`` a configuration change still notified them and, because handlers run after the service management tasks, the restart started the just-stopped unit again (and the reload failed on the inactive unit), leaving a running service although the declared state is stopped. The handlers are now gated on ``run_dovecot_service_state in ['enabled', 'running']``.
- ``run`` role - Uninstall no longer fails on Debian-family hosts where the upstream repository feature was never enabled: the apt source is removed as a plain file instead of via ``deb822_repository``, which requires the ``python3-debian`` library even for removal.
- ``run`` role - ``run_dovecot_state: "absent"`` now also removes the master-users passwd-file and the quota-warning script (at their currently configured paths plus the default paths). They were left behind before, including the password hashes in the passwd-file.

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
