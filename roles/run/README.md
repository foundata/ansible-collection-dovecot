# Ansible role: `foundata.dovecot.run`

The `foundata.dovecot.run` Ansible role (part of the `foundata.dovecot` Ansible collection). It provides automated installation, and configuration management of [Dovecot](https://dovecot.org/) across major Linux distributions.

Please note that **this role only supports Dovecot 2.4.x or newer**.



## Table of contents<a id="toc"></a>

- [Example playbooks, using this role](#examples)
- [Supported tags](#tags)<!-- ANSIBLE DOCSMITH TOC START -->
- [Role variables](#variables)
  - [`run_dovecot_state`](#variable-run_dovecot_state)
  - [`run_dovecot_autoupgrade`](#variable-run_dovecot_autoupgrade)
  - [`run_dovecot_repository_manage`](#variable-run_dovecot_repository_manage)
  - [`run_dovecot_repository_channel`](#variable-run_dovecot_repository_channel)
  - [`run_dovecot_service_state`](#variable-run_dovecot_service_state)
  - [`run_dovecot_mail_user_manage`](#variable-run_dovecot_mail_user_manage)
  - [`run_dovecot_mail_user_name`](#variable-run_dovecot_mail_user_name)
  - [`run_dovecot_mail_user_uid`](#variable-run_dovecot_mail_user_uid)
  - [`run_dovecot_mail_group_name`](#variable-run_dovecot_mail_group_name)
  - [`run_dovecot_mail_group_gid`](#variable-run_dovecot_mail_group_gid)
  - [`run_dovecot_mail_storage_path`](#variable-run_dovecot_mail_storage_path)
  - [`run_dovecot_settings`](#variable-run_dovecot_settings)
  - [`run_dovecot_settings_extra_content`](#variable-run_dovecot_settings_extra_content)
  - [`run_dovecot_passdbs`](#variable-run_dovecot_passdbs)
  - [`run_dovecot_userdbs`](#variable-run_dovecot_userdbs)
  - [`run_dovecot_external_files`](#variable-run_dovecot_external_files)
    - [`run_dovecot_external_files['master_users']`](#variable-run_dovecot_external_files-sub-master_users)
      - [`run_dovecot_external_files['master_users']['enabled']`](#variable-run_dovecot_external_files-sub-master_users-sub-enabled)
      - [`run_dovecot_external_files['master_users']['path']`](#variable-run_dovecot_external_files-sub-master_users-sub-path)
      - [`run_dovecot_external_files['master_users']['entries']`](#variable-run_dovecot_external_files-sub-master_users-sub-entries)
        - [`run_dovecot_external_files['master_users']['entries']['username']`](#variable-run_dovecot_external_files-sub-master_users-sub-entries-sub-username)
        - [`run_dovecot_external_files['master_users']['entries']['password_hash']`](#variable-run_dovecot_external_files-sub-master_users-sub-entries-sub-password_hash)
    - [`run_dovecot_external_files['quota_warning_script']`](#variable-run_dovecot_external_files-sub-quota_warning_script)
      - [`run_dovecot_external_files['quota_warning_script']['enabled']`](#variable-run_dovecot_external_files-sub-quota_warning_script-sub-enabled)
      - [`run_dovecot_external_files['quota_warning_script']['path']`](#variable-run_dovecot_external_files-sub-quota_warning_script-sub-path)
      - [`run_dovecot_external_files['quota_warning_script']['from']`](#variable-run_dovecot_external_files-sub-quota_warning_script-sub-from)
      - [`run_dovecot_external_files['quota_warning_script']['subject']`](#variable-run_dovecot_external_files-sub-quota_warning_script-sub-subject)
      - [`run_dovecot_external_files['quota_warning_script']['body']`](#variable-run_dovecot_external_files-sub-quota_warning_script-sub-body)
<!-- ANSIBLE DOCSMITH TOC END -->
- [Dependencies](#dependencies)
- [Compatibility](#compatibility)
- [External requirements](#requirements)



## Example playbooks, using this role<a id="examples"></a>

Installation with automatic upgrade:

```yaml
---

- name: "Initialize the foundata.dovecot.run role"
  hosts: localhost
  gather_facts: false
  tasks:

    - name: "Trigger invocation of the foundata.dovecot.run role"
      ansible.builtin.include_role:
        name: "foundata.dovecot.run"
      vars:
        run_dovecot_autoupgrade: true
```

Uninstall:

```yaml
---

- name: "Initialize the foundata.dovecot.run role"
  hosts: localhost
  gather_facts: false
  tasks:

    - name: "Trigger invocation of the foundata.dovecot.run role"
      ansible.builtin.include_role:
        name: "foundata.dovecot.run"
      vars:
        run_dovecot_state: "absent"
```

Minimal IMAP server with a self-contained `passwd-file` backend and Maildir storage (implicit TLS on port 993). The role ships no opinionated Dovecot defaults, so `run_dovecot_settings` mirrors Dovecot's own 2.4 grammar 1:1:

```yaml
---

- name: "Minimal Dovecot IMAP server"
  hosts: "mailservers"
  become: true
  tasks:

    - name: "Trigger invocation of the foundata.dovecot.run role"
      ansible.builtin.include_role:
        name: "foundata.dovecot.run"
      vars:
        run_dovecot_settings:
          dovecot_config_version: "2.4.0"
          dovecot_storage_version: "2.4.0"
          protocols: "imap"
          ssl: "required"
          # ADAPT to your certificate paths (e.g. provisioned by foundata.acmesh).
          ssl_server_cert_file: "/etc/ssl/certs/mail.example.org/fullchain.pem"
          ssl_server_key_file: "/etc/ssl/private/mail.example.org/cert.key"
          auth_mechanisms: "plain login"
          auth_allow_cleartext: false
          mail_driver: "maildir"
          mail_home: "/srv/vmail/%{user}"
          mail_path: "~/Maildir"
          "service imap-login":
            "inet_listener imaps":
              port: 993
              ssl: true
        # Both passdb (password check) and userdb (uid/gid/home) read the same
        # passwd-file; create /etc/dovecot/users yourself (doveadm pw -s ...).
        run_dovecot_passdbs:
          - driver: "passwd-file"
            passwd_file_path: "/etc/dovecot/users"
        run_dovecot_userdbs:
          - driver: "passwd-file"
            passwd_file_path: "/etc/dovecot/users"
```

Install from the official upstream repository (`repo.dovecot.org`) instead of the distribution packages, to pick up newer 2.4.x maintenance releases (see [`run_dovecot_repository_manage`](#variable-run_dovecot_repository_manage); currently Debian 13 only among the supported platforms):

```yaml
---

- name: "Dovecot from the upstream Community Edition (CE) repository"
  hosts: "mailservers"
  become: true
  tasks:

    - name: "Trigger invocation of the foundata.dovecot.run role"
      ansible.builtin.include_role:
        name: "foundata.dovecot.run"
      vars:
        run_dovecot_repository_manage: true
        # Optional: pin a release instead of the rolling "ce-2.4-latest" channel.
        # run_dovecot_repository_channel: "ce-2.4.4"
        run_dovecot_autoupgrade: true
        run_dovecot_settings:
          dovecot_config_version: "2.4.0"
          dovecot_storage_version: "2.4.0"
          protocols: "imap lmtp"
```

For a complete, production-style example with LDAP / Active Directory authentication, Maildir storage, per-user quota with warning mails, Sieve filtering, shared and public IMAP namespaces, see the ready-to-adapt playbook shipped with this collection: [`playbooks/mailserver_ldap_maildir.yml`](../../playbooks/mailserver_ldap_maildir.yml).



## Supported tags<a id="tags"></a>

It might be useful and faster to only call parts of the role by using tags:

- `run_dovecot_setup`: Manage basic resources, such as packages or service users.
- `run_dovecot_config`: Manage settings, such as adapting or creating configuration files.
- `run_dovecot_service`: Manage services and daemons, such as running states and service boot configurations.

There are also tags that are generally not intended to be called directly but are included for completeness and to cover edge cases:

- `run_dovecot_always`, `always`: Tasks needed by the role itself for internal role setup and the Ansible environment.


<!-- ANSIBLE DOCSMITH MAIN START -->

## Role variables<a id="variables"></a>

Main entry point for the foundata.dovecot.run role

The following variables can be configured for this role:

| Variable | Type | Required | Default | Description (abstract) |
|----------|------|----------|---------|------------------------|
| `run_dovecot_state` | `str` | No | `"present"` | Determines whether the managed resources should be `present` or `absent`.<br><br>`present` ensures that required components, such as software packages, are installed and configured.<br><br>`absent` reverts changes as much as possible, such as […](#variable-run_dovecot_state) |
| `run_dovecot_autoupgrade` | `bool` | No | `false` | If set to `true`, all managed packages will be upgraded during each Ansible run (e.g., when the package provider detects a newer version than the currently installed one). |
| `run_dovecot_repository_manage` | `bool` | No | `false` | Whether to install Dovecot from the official upstream package repository at https://repo.dovecot.org/ instead of the distribution's own packages. This role only supports Dovecot 2.4.x or newer.<br><br>`false` (the default) keeps the distribution […](#variable-run_dovecot_repository_manage) |
| `run_dovecot_repository_channel` | `str` | No | `"ce-2.4-latest"` | Which repo.dovecot.org channel to use when `run_dovecot_repository_manage` is `true`. This var's value has no effect otherwise. See https://repo.dovecot.org for details. Short summary:<br><br>- Use `ce-2.4-latest` (the default, recommended) follows […](#variable-run_dovecot_repository_channel) |
| `run_dovecot_service_state` | `str` | No | `"enabled"` | Defines the status of the service(s).<br><br>`enabled`: Service is running and will start automatically at boot.<br><br>`disabled`: Service is stopped and will not start automatically at boot.<br><br>`running`: Service is running but will not start […](#variable-run_dovecot_service_state) |
| `run_dovecot_mail_user_manage` | `bool` | No | `true` | Controls whether the role manages the system user and group that own the mail storage tree (Dovecot's conventional "vmail" identity).<br><br>When set to `true`, the role creates the group defined by `run_dovecot_mail_group_name` / […](#variable-run_dovecot_mail_user_manage) |
| `run_dovecot_mail_user_name` | `str` | No | `"vmail"` | Name of the POSIX user that owns Dovecot's mail storage tree.<br><br>Used both for `chown` on `run_dovecot_mail_storage_path` (when `run_dovecot_mail_user_manage` is `true`) and as the canonical user name you typically reference from […](#variable-run_dovecot_mail_user_name) |
| `run_dovecot_mail_user_uid` | `int` | No | `1000` | Numeric UID for the user defined by `run_dovecot_mail_user_name`.<br><br>A fixed UID makes mail storage portable between hosts (rsync, restore from backup, container migrations) without ownership rewrites. Pick a value that is free across all hosts […](#variable-run_dovecot_mail_user_uid) |
| `run_dovecot_mail_group_name` | `str` | No | `"vmail"` | Name of the POSIX group that owns Dovecot's mail storage tree.<br><br>See `run_dovecot_mail_user_name` for usage notes as the same applies here for group ownership. |
| `run_dovecot_mail_group_gid` | `int` | No | `1000` | Numeric GID for the group defined by `run_dovecot_mail_group_name`.<br><br>See `run_dovecot_mail_user_uid` for the rationale on fixed numeric IDs.<br><br>Only takes effect when `run_dovecot_mail_user_manage` is `true`. |
| `run_dovecot_mail_storage_path` | `path` | No | `"/srv/vmail"` | Absolute path to the root directory holding Dovecot's mail storage tree.<br><br>The role ensures the directory exists with ownership `run_dovecot_mail_user_name`:`run_dovecot_mail_group_name` and mode `0770`. Filesystem provisioning (partitioning, […](#variable-run_dovecot_mail_storage_path) |
| `run_dovecot_settings` | `dict` | No | `{}` | Complete Dovecot configuration as a nested YAML dictionary that mirrors Dovecot 2.4 grammar 1:1. The role renders it into a single `/etc/dovecot/dovecot.conf` (so there are no `conf.d` includes).<br><br>Rules:<br><br>- Scalar value → `key = value`. […](#variable-run_dovecot_settings) |
| `run_dovecot_settings_extra_content` | `str` | No | `""` | Raw verbatim Dovecot configuration appended at the end of `/etc/dovecot/dovecot.conf` (before the trailing `!include_try local.conf`).<br><br>This is an escape hatch of last resort for the rare case where the `run_dovecot_settings` dictionary cannot […](#variable-run_dovecot_settings_extra_content) |
| `run_dovecot_passdbs` 🔒 | `list` | No | `[]` | Ordered list of Dovecot `passdb { ... }` blocks (the password database lookup chain). One block is rendered per list entry, in declaration order — this is the authentication fallback chain Dovecot evaluates top-to-bottom.<br><br>A dedicated list is […](#variable-run_dovecot_passdbs) |
| `run_dovecot_userdbs` | `list` | No | `[]` | Ordered list of Dovecot `userdb { ... }` blocks (the user database lookup chain). One block is rendered per list entry, in declaration order.<br><br>Same rationale, structure and rendering as `run_dovecot_passdbs` (named blocks via the reserved […](#variable-run_dovecot_userdbs) |
| `run_dovecot_external_files` | `dict` | No | `{}` | Container for file artifacts Dovecot reads or runs that are NOT `dovecot.conf` itself. They have their own parsers or semantics (passwd-file format, executable scripts, ...). You could create these files with your own additional tasks (e.g. upfront, […](#variable-run_dovecot_external_files) |

### `run_dovecot_state`<a id="variable-run_dovecot_state"></a>

[*⇑ Back to ToC ⇑*](#toc)

Determines whether the managed resources should be `present` or `absent`.

`present` ensures that required components, such as software packages, are
installed and configured.

`absent` reverts changes as much as possible, such as removing packages,
deleting created users, stopping services, restoring modified settings, …

- **Type**: `str`
- **Required**: No
- **Default**: `"present"`
- **Choices**: `present`, `absent`



### `run_dovecot_autoupgrade`<a id="variable-run_dovecot_autoupgrade"></a>

[*⇑ Back to ToC ⇑*](#toc)

If set to `true`, all managed packages will be upgraded during each Ansible
run (e.g., when the package provider detects a newer version than the
currently installed one).

- **Type**: `bool`
- **Required**: No
- **Default**: `false`



### `run_dovecot_repository_manage`<a id="variable-run_dovecot_repository_manage"></a>

[*⇑ Back to ToC ⇑*](#toc)

Whether to install Dovecot from the official upstream package repository at
https://repo.dovecot.org/ instead of the distribution's own packages. This
role only supports Dovecot 2.4.x or newer.

`false` (the default) keeps the distribution repository as the package source:
the role installs whatever Dovecot version your distribution ships.

`true` configures the Dovecot Community Edition (CE) repository from
repo.dovecot.org and installs/upgrades from it. This is useful to pick up
newer releases (propably providing new features and bug fixes) than a frozen
distribution release provides.

Note on an already-installed Dovecot: enabling this changes the package
SOURCE, it does not cross-grade a package that is already installed from
the distribution. A fresh install picks the upstream build, but an
existing one is only moved to it when `run_dovecot_autoupgrade` is `true`
(or you upgrade it manually); with the default `present` state, apt keeps
the version that is already installed.

Coverage caveat: repo.dovecot.org only builds packages for a subset of the
platforms this role supports. Enabling this on a platform without upstream
coverage makes the role fail early with an explanatory message, you can still
use the Dovecot version in their default repositories, so leave this `false`
there.

See `run_dovecot_repository_channel` to choose or pin the channel.

- **Type**: `bool`
- **Required**: No
- **Default**: `false`



### `run_dovecot_repository_channel`<a id="variable-run_dovecot_repository_channel"></a>

[*⇑ Back to ToC ⇑*](#toc)

Which repo.dovecot.org channel to use when `run_dovecot_repository_manage`
is `true`. This var's value has no effect otherwise. See
https://repo.dovecot.org for details. Short summary:

- Use `ce-2.4-latest` (the default, recommended) follows the latest 2.4.x
  maintenance release.
- Pin to a specific release, such as `ce-2.4.4` for change-controlled
  deployments where package versions must not change unexpectedly. In this
  case, also consider whether it makes more sense to set
  `run_dovecot_repository_manage` to `false` and simply use your
  distribution's default packages

This role only supports Dovecot 2.4.x or newer, so using a `ce-2.3[...]`
channel won't work.

- **Type**: `str`
- **Required**: No
- **Default**: `"ce-2.4-latest"`



### `run_dovecot_service_state`<a id="variable-run_dovecot_service_state"></a>

[*⇑ Back to ToC ⇑*](#toc)

Defines the status of the service(s).

`enabled`: Service is running and will start automatically at boot.

`disabled`: Service is stopped and will not start automatically at boot.

`running`: Service is running but will not start automatically at boot.
This can be used to start a service on the first Ansible run without
enabling it for boot.

`unmanaged`: Ansible does not manage the service at all: both the
running state and the boot (enablement) state are left exactly as they
are. This is primarily useful when services are monitored and managed
by systems other than Ansible.

The singular form (`service`) is used for simplicity. However, the defined
status applies to all services if multiple are being managed by this role.

- **Type**: `str`
- **Required**: No
- **Default**: `"enabled"`
- **Choices**: `enabled`, `disabled`, `running`, `unmanaged`



### `run_dovecot_mail_user_manage`<a id="variable-run_dovecot_mail_user_manage"></a>

[*⇑ Back to ToC ⇑*](#toc)

Controls whether the role manages the system user and group that own
the mail storage tree (Dovecot's conventional "vmail" identity).

When set to `true`, the role creates the group defined by
`run_dovecot_mail_group_name` / `run_dovecot_mail_group_gid` and the
user defined by `run_dovecot_mail_user_name` / `run_dovecot_mail_user_uid`
(no login shell, no password), and ensures the directory at
`run_dovecot_mail_storage_path` exists with that ownership.

When set to `false`, the role assumes the user, group and storage
directory already exist (e.g. provisioned by another role, by your image,
or by a directory service such as LDAP/AD) and leaves them untouched.
You are then responsible for matching the UID/GID Dovecot expects.

- **Type**: `bool`
- **Required**: No
- **Default**: `true`



### `run_dovecot_mail_user_name`<a id="variable-run_dovecot_mail_user_name"></a>

[*⇑ Back to ToC ⇑*](#toc)

Name of the POSIX user that owns Dovecot's mail storage tree.

Used both for `chown` on `run_dovecot_mail_storage_path` (when
`run_dovecot_mail_user_manage` is `true`) and as the canonical user name you
typically reference from `run_dovecot_settings` (e.g. as `mail_uid`,
`mail_gid`, in `service` blocks, in `unix_listener` owners).

The role does NOT inject this name into `run_dovecot_settings` for you,
keeping the mapping explicit avoids surprises.

- **Type**: `str`
- **Required**: No
- **Default**: `"vmail"`



### `run_dovecot_mail_user_uid`<a id="variable-run_dovecot_mail_user_uid"></a>

[*⇑ Back to ToC ⇑*](#toc)

Numeric UID for the user defined by `run_dovecot_mail_user_name`.

A fixed UID makes mail storage portable between hosts (rsync, restore from
backup, container migrations) without ownership rewrites. Pick a value that is
free across all hosts that may ever access the same mail spool.

Only takes effect when `run_dovecot_mail_user_manage` is `true`.

- **Type**: `int`
- **Required**: No
- **Default**: `1000`



### `run_dovecot_mail_group_name`<a id="variable-run_dovecot_mail_group_name"></a>

[*⇑ Back to ToC ⇑*](#toc)

Name of the POSIX group that owns Dovecot's mail storage tree.

See `run_dovecot_mail_user_name` for usage notes as the same applies
here for group ownership.

- **Type**: `str`
- **Required**: No
- **Default**: `"vmail"`



### `run_dovecot_mail_group_gid`<a id="variable-run_dovecot_mail_group_gid"></a>

[*⇑ Back to ToC ⇑*](#toc)

Numeric GID for the group defined by `run_dovecot_mail_group_name`.

See `run_dovecot_mail_user_uid` for the rationale on fixed numeric IDs.

Only takes effect when `run_dovecot_mail_user_manage` is `true`.

- **Type**: `int`
- **Required**: No
- **Default**: `1000`



### `run_dovecot_mail_storage_path`<a id="variable-run_dovecot_mail_storage_path"></a>

[*⇑ Back to ToC ⇑*](#toc)

Absolute path to the root directory holding Dovecot's mail storage tree.

The role ensures the directory exists with ownership
`run_dovecot_mail_user_name`:`run_dovecot_mail_group_name` and mode
`0770`. Filesystem provisioning (partitioning, `mkfs`, mounts,
`/etc/fstab`) is explicitly out of scope for this role — mount whatever
backing storage you want at this path before running the role, or accept
that it lives on the root filesystem.

This path is not pushed into `run_dovecot_settings` automatically. Where
Dovecot needs to know it (e.g. `mail_path`, `mail_home`, namespace
`mail_path`, `auth_socket_path`, …), reference it explicitly in
`run_dovecot_settings`.

- **Type**: `path`
- **Required**: No
- **Default**: `"/srv/vmail"`



### `run_dovecot_settings`<a id="variable-run_dovecot_settings"></a>

[*⇑ Back to ToC ⇑*](#toc)

Complete Dovecot configuration as a nested YAML dictionary that mirrors
Dovecot 2.4 grammar 1:1. The role renders it into a single
`/etc/dovecot/dovecot.conf` (so there are no `conf.d` includes).

Rules:

- Scalar value → `key = value`. For settings that take a list
  (e.g. `protocols`, `mail_plugins`, `auth_mechanisms`) you can
  pass either Dovecot's native space-separated string OR a YAML
  list — the renderer joins YAML lists with a single space, so
  `protocols: ["imap", "lmtp", "sieve"]` and
  `protocols: "imap lmtp sieve"` render identically.
- Dict value → `key { ... }` (recursive).
- Block name encoded inline in the YAML key, split on first
  whitespace: `"service imap-login"`, `"namespace inbox"`,
  `'unix_listener "/var/spool/postfix/private/auth"'`. Keys without
  an inline name (e.g. `dict_server:`) render as anonymous blocks.

The `dovecot_config_version` and `dovecot_storage_version` keys are
both REQUIRED by Dovecot 2.4; the role fails fast when either is
missing. `dovecot_config_version` is always emitted as the first
non-comment line in `dovecot.conf`, regardless of where it appears
in your YAML. Set both here like any other setting.

Default is `{}` (empty) on purpose: the role ships zero opinionated
Dovecot defaults. Dovecot's compiled-in defaults supply everything you
do not set explicitly — only the two version pins above always have
to be provided.

The `mail_plugins` setting (top-level and per `"protocol <name>":`
block) is parsed at task time to derive the set of distribution
sub-packages to install. Plugin names unknown to the role's internal
plugin map cause a hard fail with the list of known names.

Example (illustrative excerpt, NOT a default the role applies):

```yaml
run_dovecot_settings:
  dovecot_config_version: "2.4.3"
  dovecot_storage_version: "2.4.3"

  protocols: "imap lmtp sieve"
  mail_plugins: "acl quota sieve"
  auth_allow_cleartext: false
  auth_mechanisms: "plain login"

  "namespace inbox":
    type: "private"
    separator: "/"
    prefix: ""
    inbox: true
    mail_driver: "maildir"
    mail_path: "~/mail/data"
    "mailbox Drafts":
      auto: "subscribe"
      special_use: '\Drafts'

  "service imap-login":
    "inet_listener imap":
      port: 143
    "inet_listener imaps":
      port: 993
      ssl: true

  "service auth":
    'unix_listener "/var/spool/postfix/private/auth"':
      mode: "0660"
      user: "postfix"
      group: "postfix"

  "protocol imap":
    mail_plugins: "$mail_plugins imap_acl imap_quota"

  'quota "User quota"':
    storage_size: "1G"

  "sieve_script personal":
    driver: "file"
    path: "~/sieve"
    active_path: "~/.dovecot.sieve"
```

Reference for all Dovecot 2.4 settings and block types:
https://doc.dovecot.org/main/core/summaries/settings.html

- **Type**: `dict`
- **Required**: No
- **Default**: `{}`



### `run_dovecot_settings_extra_content`<a id="variable-run_dovecot_settings_extra_content"></a>

[*⇑ Back to ToC ⇑*](#toc)

Raw verbatim Dovecot configuration appended at the end of
`/etc/dovecot/dovecot.conf` (before the trailing
`!include_try local.conf`).

This is an escape hatch of last resort for the rare case where the
`run_dovecot_settings` dictionary cannot express what you need
(e.g. exotic block forms that bypass the YAML-key encoding, or a
conditional include which was not known when the role was designed
or simply a yet missing feature role feature you can open an issue
for but need a solution *now*).

Prefer `run_dovecot_settings` whenever possible.

Default is `""` (empty string).

- **Type**: `str`
- **Required**: No
- **Default**: `""`



### `run_dovecot_passdbs`<a id="variable-run_dovecot_passdbs"></a>

[*⇑ Back to ToC ⇑*](#toc)

Ordered list of Dovecot `passdb { ... }` blocks (the password
database lookup chain). One block is rendered per list entry, in
declaration order — this is the authentication fallback chain
Dovecot evaluates top-to-bottom.

A dedicated list is required (instead of folding into
`run_dovecot_settings`) because the order of the lookup chain matters
and YAML dictionaries neither preserve order nor allow the duplicate
keys that repeated blocks would need.

Each entry is a dictionary that is rendered into one
`passdb <name> { ... }` block. The schema is intentionally loose: every
key/value pair becomes an inline setting (`key = value`; nested dicts
render as nested `key { ... }` blocks, YAML lists are space-joined),
with two reserved keys:

- `name`: the block's unique filter name (Dovecot 2.4 REQUIRES every
  passdb/userdb block to be named). It is used only as the block label
  and is NOT rendered as a setting. When omitted, the `driver` value is
  used as the name.
- `driver`: backend type (`static`, `passwd-file`, `pam`, `ldap`,
  `sql`, …). When omitted, Dovecot uses the block `name` as the driver.

Dovecot 2.4 removed the old `args` setting: driver-specific options are
now given as individual inline settings instead (e.g. `password` for
`static`, `passwd_file_path` for `passwd-file`, the LDAP connection
settings for `ldap`, …). Refer to
https://doc.dovecot.org/main/core/config/auth/passdb.html for the full
list of per-driver settings and their semantics.

IMPORTANT: some settings are *driver-scoped* and Dovecot only accepts
them by their short name when the block name equals the driver (e.g.
`static`'s `password`). For those drivers, leave `name` unset so it
defaults to the driver. Settings that are not driver-scoped (e.g.
`passwd-file`'s `passwd_file_path`) work under any custom `name`, which
you need when declaring several blocks of the same driver.

Example:

```yaml
run_dovecot_passdbs:
  # "name" omitted -> defaults to the driver ("passdb static { ... }"),
  # required so the driver-scoped "password" resolves by its short name.
  - driver: "static"
    password: "{PLAIN}changeme"
  # A custom "name" allows several blocks of the same driver; only use
  # settings that are not driver-scoped (passwd_file_path qualifies).
  - name: "master"
    driver: "passwd-file"
    passwd_file_path: "/etc/dovecot/passwd.masterusers"
```

Entries commonly carry credentials (`static`'s `password`, LDAP bind
passwords, SQL connection strings), so the complete list is marked
`no_log`: argument validation failures and verbose task output show
it censored. Keep the actual secrets in Ansible Vault or another
secret source nevertheless.

- **Type**: `list`
- **Required**: No
- **Sensitive**: Yes (`no_log`, values are masked in logs)
- **Default**: `[]`
- **List Elements**: `dict`



### `run_dovecot_userdbs`<a id="variable-run_dovecot_userdbs"></a>

[*⇑ Back to ToC ⇑*](#toc)

Ordered list of Dovecot `userdb <name> { ... }` blocks (the user
database lookup chain). One block is rendered per list entry, in
declaration order.

Same rationale, structure and rendering as `run_dovecot_passdbs` (named
blocks via the reserved `name` key, `driver` selecting the backend, all
other keys rendered as inline settings, no `args`), see there for
details. For the `static` driver the synthetic user identity is given
as a nested `fields { ... }` block (`uid`, `gid`, `home`, …).

Refer to https://doc.dovecot.org/main/core/config/auth/userdb.html
for the full list of per-driver settings and their semantics.

Example:

```yaml
run_dovecot_userdbs:
  # "name" omitted -> defaults to the driver ("userdb static { ... }").
  - driver: "static"
    fields:
      uid: "vmail"
      gid: "vmail"
      home: "/srv/vmail/%{user}"
```

- **Type**: `list`
- **Required**: No
- **Default**: `[]`
- **List Elements**: `dict`



### `run_dovecot_external_files`<a id="variable-run_dovecot_external_files"></a>

[*⇑ Back to ToC ⇑*](#toc)

Container for file artifacts Dovecot reads or runs that are NOT `dovecot.conf`
itself. They have their own parsers or semantics (passwd-file format,
executable scripts, ...). You could create these files with your own
additional tasks (e.g. upfront, before calling the role), but this variable
exposes some handy helpers for commonly needed use cases.

Note: LDAP is configured entirely inline in `run_dovecot_passdbs` /
`run_dovecot_userdbs` (`driver: ldap` plus `uris`, `dn`, `dnpass`,
`base`, `user_filter`, `pass_filter`, `user_attrs`, ...). Dovecot 2.4 no
longer uses a separate `dovecot-ldap.conf.ext` file, so there is no LDAP
helper here.

Top-level keys are constrained to the closed set declared as suboptions
below — unknown keys are rejected by Ansible's argument-spec validation at
role start. For each known key, an `enabled` boolean controls the role's
behaviour:

- omitting the key entirely → the role ignores that artifact (the file
  is neither created nor removed);
- declaring the key with `enabled: true` (the default when the key is
  present) → the role renders/manages the file;
- declaring the key with `enabled: false` → the role removes the file
  if it exists (handy for clean uninstall / staged migrations without
  having to delete YAML around it).

Example:

```yaml
run_dovecot_external_files:
  master_users:
    enabled: true
    path: "/etc/dovecot/passwd.masterusers"      # role-default; can be omitted
    entries:
      - username: "admin"
        password_hash: "{BLF-CRYPT}$2y$05$..."
  quota_warning_script:
    enabled: true
    path: "/usr/local/bin/dovecot-quota-warning.sh"
    from: "postmaster@example.com"
    subject: "Quota warning (${PERCENT}% full)"
    body: |
      EN: Your mailbox is now ${PERCENT}% full.
      DE: Ihr Postfach ist zu ${PERCENT}% belegt.
```

- **Type**: `dict`
- **Required**: No
- **Default**: `{}`

#### `run_dovecot_external_files['master_users']`<a id="variable-run_dovecot_external_files-sub-master_users"></a>

[*⇑ Back to ToC ⇑*](#toc)

Renders Dovecot's master-users passwd-file at `path`. The file is
consumed by a `passdb` entry with `driver: passwd-file` and
`master: true`.

The role does NOT hash passwords. Generate the hash out of band with
`doveadm pw -s BLF-CRYPT` (Dovecot's recommended scheme) or `htpasswd -B`
and place the resulting `{SCHEME}hash` string into `password_hash`.

`enabled: false` removes the file if present.

- **Type**: `dict`
- **Required**: No

##### `run_dovecot_external_files['master_users']['enabled']`<a id="variable-run_dovecot_external_files-sub-master_users-sub-enabled"></a>

[*⇑ Back to ToC ⇑*](#toc)

When `true` (the default), the role renders the file at `path`.
When `false`, the role removes the file if present.

- **Type**: `bool`
- **Required**: No
- **Default**: `true`

##### `run_dovecot_external_files['master_users']['path']`<a id="variable-run_dovecot_external_files-sub-master_users-sub-path"></a>

[*⇑ Back to ToC ⇑*](#toc)

Absolute path where the rendered passwd-file is written. The
same path must be referenced from a `run_dovecot_passdbs` entry
using `driver: passwd-file` with `passwd_file_path: "<this path>"`.

Conventions differ by distribution (Debian historically uses
`/etc/dovecot/master-users`, RHEL/Fedora `/etc/dovecot/passwd.masterusers`).
The role deliberately uses the same default on every platform
so the path you reference from `run_dovecot_passdbs` stays
stable. Override here if you prefer another location.

- **Type**: `str`
- **Required**: No
- **Default**: `"/etc/dovecot/passwd.masterusers"`

##### `run_dovecot_external_files['master_users']['entries']`<a id="variable-run_dovecot_external_files-sub-master_users-sub-entries"></a>

[*⇑ Back to ToC ⇑*](#toc)

List of master-user entries to write into the passwd-file.
Each entry maps to one line in `username:{SCHEME}hash` form.

- **Type**: `list`
- **Required**: No
- **List Elements**: `dict`

###### `run_dovecot_external_files['master_users']['entries']['username']`<a id="variable-run_dovecot_external_files-sub-master_users-sub-entries-sub-username"></a>

[*⇑ Back to ToC ⇑*](#toc)

Master-user login name. Authenticated as
`<real-user>*<username>` against Dovecot per the master
user convention.

- **Type**: `str`
- **Required**: No

###### `run_dovecot_external_files['master_users']['entries']['password_hash']`<a id="variable-run_dovecot_external_files-sub-master_users-sub-entries-sub-password_hash"></a>

[*⇑ Back to ToC ⇑*](#toc)

Full Dovecot password string including the scheme prefix
in curly braces, e.g. `{BLF-CRYPT}$2y$05$...`. The role
writes the value verbatim; it does not hash or validate
the scheme.

The field is marked `no_log`, so argument validation
failures and verbose task output show it censored. Keep
the value in Ansible Vault or another secret source
nevertheless.

- **Type**: `str`
- **Required**: No
- **Sensitive**: Yes (`no_log`, values are masked in logs)



#### `run_dovecot_external_files['quota_warning_script']`<a id="variable-run_dovecot_external_files-sub-quota_warning_script"></a>

[*⇑ Back to ToC ⇑*](#toc)

Renders a shell script that Dovecot's
`"service quota-warning": { executable: "script <path>" ... }`
can invoke when a user crosses a configured quota threshold. The script is
deployed with mode `0755`, owner `root`, group `root`.

The script is independent of the `service quota-warning` block itself, you
still need to declare that service inside `run_dovecot_settings` and
reference this `path` from its `executable` setting.

Declare that service with `user: "root"`: the script invokes
`dovecot-lda`, which has to read the root-only `dovecot.conf` (mode
`0600`, may contain credentials) and switch to the mail user - both fail
under any non-root service user, silently swallowing the warning mails.

`enabled: false` removes the file if present (note: it does NOT remove
the corresponding `service quota-warning` block from
`run_dovecot_settings` — manage that explicitly there).

- **Type**: `dict`
- **Required**: No

##### `run_dovecot_external_files['quota_warning_script']['enabled']`<a id="variable-run_dovecot_external_files-sub-quota_warning_script-sub-enabled"></a>

[*⇑ Back to ToC ⇑*](#toc)

When `true` (the default), the role renders the script at `path`.
When `false`, the role removes the file if present.

- **Type**: `bool`
- **Required**: No
- **Default**: `true`

##### `run_dovecot_external_files['quota_warning_script']['path']`<a id="variable-run_dovecot_external_files-sub-quota_warning_script-sub-path"></a>

[*⇑ Back to ToC ⇑*](#toc)

Absolute path where the rendered script is written. The same path must
be referenced from
`run_dovecot_settings['service quota-warning']['executable']`
(as `script <path>` value).

- **Type**: `path`
- **Required**: No

##### `run_dovecot_external_files['quota_warning_script']['from']`<a id="variable-run_dovecot_external_files-sub-quota_warning_script-sub-from"></a>

[*⇑ Back to ToC ⇑*](#toc)

`From:` header used in the warning mail the script sends.

Like `subject` and `body`, the value is inserted into the
script as a quoted literal and NOT interpreted by a shell:
`${PERCENT}` is the only placeholder substituted at runtime,
command substitutions such as `$(...)` end up in the mail as
literal text. Line breaks for `from` and `subject` are not
allowed because mail header values must be single-line.

- **Type**: `str`
- **Required**: No

##### `run_dovecot_external_files['quota_warning_script']['subject']`<a id="variable-run_dovecot_external_files-sub-quota_warning_script-sub-subject"></a>

[*⇑ Back to ToC ⇑*](#toc)

`Subject:` header used in the warning mail. Dovecot's
`${PERCENT}` placeholder is substituted by the script at
runtime (see `from` for the escaping rules).

- **Type**: `str`
- **Required**: No

##### `run_dovecot_external_files['quota_warning_script']['body']`<a id="variable-run_dovecot_external_files-sub-quota_warning_script-sub-body"></a>

[*⇑ Back to ToC ⇑*](#toc)

Message body of the warning mail. Multi-line strings are
written verbatim; `${PERCENT}` placeholders are substituted
by the script at runtime (see `from` for the escaping
rules).

- **Type**: `str`
- **Required**: No
- **Default**: `"EN: Your mailbox is now ${PERCENT}% full.
DE: Ihr Postfach ist zu ${PERCENT}% belegt.
FR: Votre boîte est pleine à ${PERCENT}%
"`





<!-- ANSIBLE DOCSMITH MAIN END -->

## Dependencies<a id="dependencies"></a>

See `dependencies` in [`meta/main.yml`](./meta/main.yml).



## Compatibility<a id="compatibility"></a>

See `min_ansible_version` in [`meta/main.yml`](./meta/main.yml) and `__run_dovecot_supported_platforms` in [`vars/main.yml`](./vars/main.yml).



## External requirements<a id="requirements"></a>

There are no special requirements not covered by Ansible itself.
