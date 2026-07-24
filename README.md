# Ansible collection: `foundata.dovecot`

This repository contains the `foundata.dovecot` Ansible Collection.

It provides resources to manage and use [Dovecot](https://dovecot.org/), a secure and high-performance open-source IMAP and POP3 server (mail server).


<div align="center" id="project-readme-header">
<br>
<br>

**⭐ Found this useful? Support open-source and star this project:**

[![GitHub repository](https://img.shields.io/github/stars/foundata/ansible-collection-dovecot.svg)](https://github.com/foundata/ansible-collection-dovecot)

<br>
</div>



## Table of contents<a id="toc"></a>

- [Included content](#content)
- [Dependencies](#dependencies)
- [Licensing, copyright](#licensing-copyright)
- [Author information](#author-information)



## Included content<a id="content"></a>

### Role: `foundata.dovecot.run`

The primary role in this collection to configure and maintain Dovecot. [Its `README.md`](./roles/run/README.md) covers configuration, usage examples, and more:

<!-- ANSIBLE DOCSMITH TOC-FULL run START -->
- [Ansible role: `foundata.dovecot.run`](roles/run/README.md#ansible-role-foundatadovecotrun)
  - [Table of contents](roles/run/README.md#toc)
  - [Example playbooks, using this role](roles/run/README.md#examples)
  - [Supported tags](roles/run/README.md#tags)
  - [Role variables](roles/run/README.md#variables)
    - [`run_dovecot_state`](roles/run/README.md#variable-run_dovecot_state)
    - [`run_dovecot_autoupgrade`](roles/run/README.md#variable-run_dovecot_autoupgrade)
    - [`run_dovecot_repository_manage`](roles/run/README.md#variable-run_dovecot_repository_manage)
    - [`run_dovecot_repository_channel`](roles/run/README.md#variable-run_dovecot_repository_channel)
    - [`run_dovecot_service_state`](roles/run/README.md#variable-run_dovecot_service_state)
    - [`run_dovecot_mail_user_manage`](roles/run/README.md#variable-run_dovecot_mail_user_manage)
    - [`run_dovecot_mail_user_name`](roles/run/README.md#variable-run_dovecot_mail_user_name)
    - [`run_dovecot_mail_user_uid`](roles/run/README.md#variable-run_dovecot_mail_user_uid)
    - [`run_dovecot_mail_group_name`](roles/run/README.md#variable-run_dovecot_mail_group_name)
    - [`run_dovecot_mail_group_gid`](roles/run/README.md#variable-run_dovecot_mail_group_gid)
    - [`run_dovecot_mail_storage_path`](roles/run/README.md#variable-run_dovecot_mail_storage_path)
    - [`run_dovecot_settings`](roles/run/README.md#variable-run_dovecot_settings)
    - [`run_dovecot_settings_extra_content`](roles/run/README.md#variable-run_dovecot_settings_extra_content)
    - [`run_dovecot_passdbs`](roles/run/README.md#variable-run_dovecot_passdbs)
    - [`run_dovecot_userdbs`](roles/run/README.md#variable-run_dovecot_userdbs)
    - [`run_dovecot_external_files`](roles/run/README.md#variable-run_dovecot_external_files)
      - [`run_dovecot_external_files['master_users']`](roles/run/README.md#variable-run_dovecot_external_files-sub-master_users)
        - [`run_dovecot_external_files['master_users']['enabled']`](roles/run/README.md#variable-run_dovecot_external_files-sub-master_users-sub-enabled)
        - [`run_dovecot_external_files['master_users']['path']`](roles/run/README.md#variable-run_dovecot_external_files-sub-master_users-sub-path)
        - [`run_dovecot_external_files['master_users']['entries']`](roles/run/README.md#variable-run_dovecot_external_files-sub-master_users-sub-entries)
          - [`run_dovecot_external_files['master_users']['entries']['username']`](roles/run/README.md#variable-run_dovecot_external_files-sub-master_users-sub-entries-sub-username)
          - [`run_dovecot_external_files['master_users']['entries']['password_hash']`](roles/run/README.md#variable-run_dovecot_external_files-sub-master_users-sub-entries-sub-password_hash)
      - [`run_dovecot_external_files['quota_warning_script']`](roles/run/README.md#variable-run_dovecot_external_files-sub-quota_warning_script)
        - [`run_dovecot_external_files['quota_warning_script']['enabled']`](roles/run/README.md#variable-run_dovecot_external_files-sub-quota_warning_script-sub-enabled)
        - [`run_dovecot_external_files['quota_warning_script']['path']`](roles/run/README.md#variable-run_dovecot_external_files-sub-quota_warning_script-sub-path)
        - [`run_dovecot_external_files['quota_warning_script']['from']`](roles/run/README.md#variable-run_dovecot_external_files-sub-quota_warning_script-sub-from)
        - [`run_dovecot_external_files['quota_warning_script']['subject']`](roles/run/README.md#variable-run_dovecot_external_files-sub-quota_warning_script-sub-subject)
        - [`run_dovecot_external_files['quota_warning_script']['body']`](roles/run/README.md#variable-run_dovecot_external_files-sub-quota_warning_script-sub-body)
  - [Dependencies](roles/run/README.md#dependencies)
  - [Compatibility](roles/run/README.md#compatibility)
  - [External requirements](roles/run/README.md#requirements)
<!-- ANSIBLE DOCSMITH TOC-FULL run END -->



### Playbook: `mailserver_ldap_maildir.yml`

[`mailserver_ldap_maildir.yml`](./playbooks/mailserver_ldap_maildir.yml) is a complete Dovecot mailserver setup - IMAP/LMTP/ManageSieve, Active Directory (LDAP) authentication, Maildir storage, per-user quota with warning mails, Sieve filtering and shared and public IMAP namespaces.

The deployment-specific values (mail domain, TLS certificate paths, LDAP/AD coordinates, mail storage, quota) are exposed as `dovecot_playbook_*` variables you override per environment.

Once the collection is installed (`ansible-galaxy collection install foundata.dovecot`), you can run the shipped playbook directly by its fully qualified name. Point it at your own inventory, supply the `dovecot_playbook_*` variables (e.g. via `group_vars` / `host_vars` / `--extra-vars`):

```sh
ansible-playbook foundata.dovecot.mailserver_ldap_maildir --inventory ./inventory
```

Alternatively, copy it into your own playbook repository and edit it to your needs.



## Dependencies<a id="dependencies"></a>

See `dependencies` in [`galaxy.yml`](./galaxy.yml).



## Licensing, copyright<a id="licensing-copyright"></a>

<!--REUSE-IgnoreStart-->
Copyright (c) 2026 foundata GmbH

This project is licensed under the GNU General Public License v3.0 or later (SPDX-License-Identifier: `GPL-3.0-or-later`), see [`LICENSES/GPL-3.0-or-later.txt`](LICENSES/GPL-3.0-or-later.txt) for the full text.

The [`REUSE.toml`](REUSE.toml) file provides detailed licensing and copyright information in a human- and machine-readable format. This includes parts that may be subject to different licensing or usage terms, such as third-party components. The repository conforms to the [REUSE specification](https://reuse.software/spec/). You can use [`reuse spdx`](https://reuse.readthedocs.io/en/latest/readme.html#cli) to create a [SPDX software bill of materials (SBOM)](https://en.wikipedia.org/wiki/Software_Package_Data_Exchange).
<!--REUSE-IgnoreEnd-->



## Author information<a id="author-information"></a>

This project was created and is maintained by foundata GmbH.

Initially based on an [Ansible skeleton](https://foundata.com/en/projects/ansible-skeletons/) developed by [foundata](https://foundata.com/).
