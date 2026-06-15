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

The primary resource in this collection to configure and maintain Dovecot. See the [role's `README.md`](./roles/run/README.md) for more information and usage examples.



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
