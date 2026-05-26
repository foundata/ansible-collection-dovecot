# Ansible role: `foundata.dovecot.run`

The `foundata.dovecot.run` Ansible role (part of the `foundata.dovecot` Ansible collection).



## Table of contents<a id="toc"></a>

- [Example playbooks, using this role](#examples)
- [Supported tags](#tags)<!-- ANSIBLE DOCSMITH TOC START -->
- [Role variables](#variables)
  - [`run_dovecot_state`](#variable-run_dovecot_state)
  - [`run_dovecot_autoupgrade`](#variable-run_dovecot_autoupgrade)
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
    - [`run_dovecot_external_files['ldap']`](#variable-run_dovecot_external_files-sub-ldap)
      - [`run_dovecot_external_files['ldap']['enabled']`](#variable-run_dovecot_external_files-sub-ldap-sub-enabled)
      - [`run_dovecot_external_files['ldap']['path']`](#variable-run_dovecot_external_files-sub-ldap-sub-path)
      - [`run_dovecot_external_files['ldap']['settings']`](#variable-run_dovecot_external_files-sub-ldap-sub-settings)
      - [`run_dovecot_external_files['ldap']['userdb_separate_file']`](#variable-run_dovecot_external_files-sub-ldap-sub-userdb_separate_file)
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



## Supported tags<a id="tags"></a>

It might be useful and faster to only call parts of the role by using tags:

- `run_dovecot_setup`: Manage basic resources, such as packages or service users.
- `run_dovecot_config`: Manage settings, such as adapting or creating configuration files.
- `run_dovecot_service`: Manage services and daemons, such as running states and service boot configurations.

There are also tags usually not meant to be called directly but listed for the sake of completeness** and edge cases:

- `run_dovecot_always`, `always`: Tasks needed by the role itself for internal role setup and the Ansible environment.


<!-- ANSIBLE DOCSMITH MAIN START -->

## Role variables<a id="variables"></a>

The following variables can be configured for this role:

| Variable | Type | Required | Default | Description (abstract) |
|----------|------|----------|---------|------------------------|
| `run_dovecot_state` | `str` | No | `"present"` | Determines whether the managed resources should be `present` or `absent`.<br><br>`present` ensures that required components, such as software packages, are installed and configured.<br><br>`absent` reverts changes as much as possible, such as […](#variable-run_dovecot_state) |
| `run_dovecot_autoupgrade` | `bool` | No | `false` | If set to `true`, all managed packages will be upgraded during each Ansible run (e.g., when the package provider detects a newer version than the currently installed one). |
| `run_dovecot_service_state` | `str` | No | `"enabled"` | Defines the status of the service(s).<br><br>`enabled`: Service is running and will start automatically at boot.<br><br>`disabled`: Service is stopped and will not start automatically at boot.<br><br>`running`: Service is running but will not start […](#variable-run_dovecot_service_state) |
| `run_dovecot_mail_user_manage` | `bool` | No | `true` | Controls whether the role manages the system user and group that own the mail storage tree (Dovecot's conventional "vmail" identity).<br><br>When set to `true`, the role creates the group defined by `run_dovecot_mail_group_name` / […](#variable-run_dovecot_mail_user_manage) |
| `run_dovecot_mail_user_name` | `str` | No | `"vmail"` | Name of the POSIX user that owns Dovecot's mail storage tree.<br><br>Used both for `chown` on `run_dovecot_mail_storage_path` (when `run_dovecot_mail_user_manage` is `true`) and as the canonical user name you typically reference from […](#variable-run_dovecot_mail_user_name) |
| `run_dovecot_mail_user_uid` | `int` | No | `1000` | Numeric UID for the user defined by `run_dovecot_mail_user_name`.<br><br>A fixed UID makes mail storage portable between hosts (rsync, restore from backup, container migrations) without ownership rewrites. Pick a value that is free across all hosts […](#variable-run_dovecot_mail_user_uid) |
| `run_dovecot_mail_group_name` | `str` | No | `"vmail"` | Name of the POSIX group that owns Dovecot's mail storage tree.<br><br>See `run_dovecot_mail_user_name` for usage notes as the same applies here for group ownership. |
| `run_dovecot_mail_group_gid` | `int` | No | `1000` | Numeric GID for the group defined by `run_dovecot_mail_group_name`.<br><br>See `run_dovecot_mail_user_uid` for the rationale on fixed numeric IDs.<br><br>Only takes effect when `run_dovecot_mail_user_manage` is `true`. |
| `run_dovecot_mail_storage_path` | `path` | No | `"/srv/vmail"` | Absolute path to the root directory holding Dovecot's mail storage tree.<br><br>The role ensures the directory exists with ownership `run_dovecot_mail_user_name`:`run_dovecot_mail_group_name` and mode `0770`. Filesystem provisioning (partitioning, […](#variable-run_dovecot_mail_storage_path) |
| `run_dovecot_settings` | `dict` | No | `{}` | Complete Dovecot configuration as a nested YAML dictionary that mirrors Dovecot 2.4 grammar 1:1. The role renders it into a single `/etc/dovecot/dovecot.conf` (so there are no `conf.d` includes).<br><br>Rules:<br><br>- Scalar value → `key = value`. […](#variable-run_dovecot_settings) |
| `run_dovecot_settings_extra_content` | `str` | No | `""` | Raw verbatim Dovecot configuration appended at the end of `/etc/dovecot/dovecot.conf` (before the trailing `!include_try local.conf`).<br><br>This is an escape hatch of last resort for the rare case where the `run_dovecot_settings` dictionary cannot […](#variable-run_dovecot_settings_extra_content) |
| `run_dovecot_passdbs` | `list` | No | `[]` | Ordered list of Dovecot `passdb { ... }` blocks (the password database lookup chain). One block is rendered per list entry, in declaration order — this is the authentication fallback chain Dovecot evaluates top-to-bottom.<br><br>A dedicated list is […](#variable-run_dovecot_passdbs) |
| `run_dovecot_userdbs` | `list` | No | `[]` | Ordered list of Dovecot `userdb { ... }` blocks (the user database lookup chain). One block is rendered per list entry, in declaration order.<br><br>Same rationale, structure and rendering as `run_dovecot_passdbs`, see there for details. Common entry […](#variable-run_dovecot_userdbs) |
| `run_dovecot_external_files` | `dict` | No | `{}` | Container for file artifacts Dovecot reads or runs that are NOT `dovecot.conf` itself. They have their own parsers or semantics (LDAP `.conf.ext` syntax, passwd-file format, executable scripts, ...). You could create these files with your own […](#variable-run_dovecot_external_files) |

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



### `run_dovecot_service_state`<a id="variable-run_dovecot_service_state"></a>

[*⇑ Back to ToC ⇑*](#toc)

Defines the status of the service(s).

`enabled`: Service is running and will start automatically at boot.

`disabled`: Service is stopped and will not start automatically at boot.

`running`: Service is running but will not start automatically at boot.
This can be used to start a service on the first Ansible run without
enabling it for boot.

`unmanaged`: Service will not start at boot, and Ansible will not manage
its running state. This is primarily useful when services are monitored
and managed by systems other than Ansible.

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
  `'unix_listener "/var/spool/postfix/private/auth"'`, `plugin:`
  (anonymous).

The `dovecot_config_version` key (which Dovecot 2.4 REQUIRES) is always
emitted as the first non-comment line in `dovecot.conf`, regardless of
where it appears in your YAML. Set it (and `dovecot_storage_version`,
if you want to pin the on-disk storage format too) here like any other
setting.

Default is `{}` (empty) on purpose: the role ships zero opinionated
Dovecot defaults. Dovecot's compiled-in defaults supply everything you
do not set explicitly.

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
  disable_plaintext_auth: true
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

  plugin:
    sieve: "file:~/sieve;active=~/sieve.active"
    quota: "dict:User quota::file:%h/dovecot-quota"
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
`run_dovecot_settings`) because `passdb` blocks have no name
attribute and ordering matters — properties YAML dictionaries
cannot guarantee.

Each entry is a dictionary whose key/value pairs are rendered as
`key = value` inside the block. The schema is intentionally loose;
common keys include:

- `driver` (required): backend type (`ldap`, `passwd-file`, `pam`,
  `passwd`, `shadow`, `static`, …)
- `args`: backend-specific argument string (e.g. path to a
  `.conf.ext` for LDAP, path to a passwd-file)
- `master`: `true` to mark this passdb as supplying master users
- `result_success`, `result_failure`, `result_internalfail`:
  chain control (`return-ok`, `return-fail`, `continue`,
  `continue-ok`, `continue-fail`)
- `default_fields`, `override_fields`, `mechanisms`, `username_filter`,
  `auth_verbose`, …

Refer to https://doc.dovecot.org/main/core/config/auth/passdb.html
for the full list of fields and their semantics.

Example:

```yaml
run_dovecot_passdbs:
  - driver: "ldap"
    args: "/etc/dovecot/dovecot-ldap.conf.ext"
  - driver: "passwd-file"
    args: "/etc/dovecot/master-users"
    master: true
    result_success: "continue"
```

- **Type**: `list`
- **Required**: No
- **Default**: `[]`
- **List Elements**: `dict`



### `run_dovecot_userdbs`<a id="variable-run_dovecot_userdbs"></a>

[*⇑ Back to ToC ⇑*](#toc)

Ordered list of Dovecot `userdb { ... }` blocks (the user database lookup
chain). One block is rendered per list entry, in declaration order.

Same rationale, structure and rendering as `run_dovecot_passdbs`, see there
for details. Common entry keys include `driver`, `args`, `default_fields`,
`override_fields`, `result_*`, ...

Refer to https://doc.dovecot.org/main/core/config/auth/userdb.html
for the full list of fields and their semantics.

Example:

```yaml
run_dovecot_userdbs:
  - driver: "ldap"
    args: "/etc/dovecot/dovecot-ldap-userdb.conf.ext"
  - driver: "static"
    args: "uid=vmail gid=vmail home=/srv/vmail/%u"
```

- **Type**: `list`
- **Required**: No
- **Default**: `[]`
- **List Elements**: `dict`



### `run_dovecot_external_files`<a id="variable-run_dovecot_external_files"></a>

[*⇑ Back to ToC ⇑*](#toc)

Container for file artifacts Dovecot reads or runs that are NOT `dovecot.conf`
itself. They have their own parsers or semantics (LDAP `.conf.ext` syntax,
passwd-file format, executable scripts, ...). You could create these files
with your own additional tasks (e.g. upfront, before calling the role), but
this variable exposes some handy helpers for commonly needed use cases.

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
  ldap:
    enabled: true
    path: "/etc/dovecot/dovecot-ldap.conf.ext"   # role-default; can be omitted
    settings:
      uris: "ldap://ldap.example.com:389"
      tls: false
      auth_bind: true
      dn: "CN=svc,DC=example,DC=com"
      dnpass: "{{ lookup('ansible.builtin.unvault', '...') | trim }}"
      base: "OU=Users,DC=example,DC=com"
      scope: "subtree"
      user_filter: "(&(objectClass=user)(mail=%u))"
      pass_filter: "(&(objectClass=user)(mail=%u))"
      user_attrs: "..."
      blocking: true
    userdb_separate_file: true
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

#### `run_dovecot_external_files['ldap']`<a id="variable-run_dovecot_external_files-sub-ldap"></a>

[*⇑ Back to ToC ⇑*](#toc)

Renders Dovecot's LDAP backend configuration into the file pointed to
by `path`. The LDAP file uses Dovecot's alternate parser: no
`!include`, no `$variables`, raw `key = value` lines only.

The rendered file is typically referenced from `run_dovecot_passdbs` /
`run_dovecot_userdbs` via `args: "<path>"`. When
`userdb_separate_file` is `true`, a sibling file is rendered next to
`path` with `-userdb` inserted before the extension (e.g.
`dovecot-ldap.conf.ext` → `dovecot-ldap-userdb.conf.ext`) to support
split passdb/userdb lookups.

`enabled: false` removes the file (and, if applicable, the sibling
`*-userdb.conf.ext`) if present.

- **Type**: `dict`
- **Required**: No

##### `run_dovecot_external_files['ldap']['enabled']`<a id="variable-run_dovecot_external_files-sub-ldap-sub-enabled"></a>

[*⇑ Back to ToC ⇑*](#toc)

When `true` (the default), the role renders the file at `path`.
When `false`, the role removes the file (and the sibling
`*-userdb.conf.ext` if `userdb_separate_file: true`) if present.

- **Type**: `bool`
- **Required**: No
- **Default**: `true`

##### `run_dovecot_external_files['ldap']['path']`<a id="variable-run_dovecot_external_files-sub-ldap-sub-path"></a>

[*⇑ Back to ToC ⇑*](#toc)

Absolute path where the rendered LDAP configuration is written.
The same path must be referenced from the corresponding
`run_dovecot_passdbs` / `run_dovecot_userdbs` entries via their
`args` key. When `userdb_separate_file` is `true`, the sibling
userdb file is written next to this path with `-userdb` inserted
before its extension.

- **Type**: `str`
- **Required**: No
- **Default**: `"/etc/dovecot/dovecot-ldap.conf.ext"`

##### `run_dovecot_external_files['ldap']['settings']`<a id="variable-run_dovecot_external_files-sub-ldap-sub-settings"></a>

[*⇑ Back to ToC ⇑*](#toc)

Raw key/value pairs written verbatim into the rendered file.

Keys are intentionally NOT enumerated by this role. Refer to
https://doc.dovecot.org/main/core/config/auth/passdb_ldap.html
for the full list of supported keys (`uris`, `dn`, `dnpass`,
`base`, `scope`, `user_filter`, `pass_filter`, `user_attrs`,
`auth_bind`, `tls`, `blocking`, ...).

- **Type**: `dict`
- **Required**: No

##### `run_dovecot_external_files['ldap']['userdb_separate_file']`<a id="variable-run_dovecot_external_files-sub-ldap-sub-userdb_separate_file"></a>

[*⇑ Back to ToC ⇑*](#toc)

When `true`, the role also renders a parallel
`*-userdb.conf.ext` (same `settings`, name derived from `path`)
so that a separate `userdb { driver: ldap, args: <that-path> }`
block can be configured. Useful when passdb and userdb queries
need different filters/attributes or to keep them
independently overridable.

- **Type**: `bool`
- **Required**: No
- **Default**: `false`


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
using `driver: passwd-file`, `master: true`, `args: "<this path>"`.

Conventions differ by distribution (Debian historically uses
`/etc/dovecot/master-users`, RHEL/Fedora `/etc/dovecot/passwd.masterusers`);
the role's per-platform `vars/<platform>.yml` overrides the
default below to match the local convention. Override here only
if you want a non-standard location.

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

Keep the value in Ansible Vault or another secret source
— this field is NOT marked `no_log`.

- **Type**: `str`
- **Required**: No



#### `run_dovecot_external_files['quota_warning_script']`<a id="variable-run_dovecot_external_files-sub-quota_warning_script"></a>

[*⇑ Back to ToC ⇑*](#toc)

Renders a shell script that Dovecot's
`"service quota-warning": { executable: "script <path>" ... }`
can invoke when a user crosses a configured quota threshold. The script is
deployed with mode `0755`, owner `root`, group `root`.

The script is independent of the `service quota-warning` block itself, you
still need to declare that service inside `run_dovecot_settings` and
reference this `path` from its `executable` setting.

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

- **Type**: `str`
- **Required**: No

##### `run_dovecot_external_files['quota_warning_script']['subject']`<a id="variable-run_dovecot_external_files-sub-quota_warning_script-sub-subject"></a>

[*⇑ Back to ToC ⇑*](#toc)

`Subject:` header used in the warning mail. Dovecot's
`${PERCENT}` placeholder is expanded by the script at
runtime.

- **Type**: `str`
- **Required**: No

##### `run_dovecot_external_files['quota_warning_script']['body']`<a id="variable-run_dovecot_external_files-sub-quota_warning_script-sub-body"></a>

[*⇑ Back to ToC ⇑*](#toc)

Message body of the warning mail. Multi-line strings are
written verbatim; `${PERCENT}` placeholders are expanded
by the script at runtime.

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
