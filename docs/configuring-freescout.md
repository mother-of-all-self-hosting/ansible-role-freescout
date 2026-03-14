<!--
SPDX-FileCopyrightText: 2020 Aaron Raimist
SPDX-FileCopyrightText: 2020 Chris van Dijk
SPDX-FileCopyrightText: 2020 Dominik Zajac
SPDX-FileCopyrightText: 2020 Mickaël Cornière
SPDX-FileCopyrightText: 2020-2024 MDAD project contributors
SPDX-FileCopyrightText: 2020-2024 Slavi Pantaleev
SPDX-FileCopyrightText: 2022 François Darveau
SPDX-FileCopyrightText: 2022 Julian Foad
SPDX-FileCopyrightText: 2022 Warren Bailey
SPDX-FileCopyrightText: 2023 Antonis Christofides
SPDX-FileCopyrightText: 2023 Felix Stupp
SPDX-FileCopyrightText: 2023 Julian-Samuel Gebühr
SPDX-FileCopyrightText: 2023 Pierre 'McFly' Marty
SPDX-FileCopyrightText: 2024 Nikita Chernyi
SPDX-FileCopyrightText: 2024 Thomas Miceli
SPDX-FileCopyrightText: 2024-2026 Suguru Hirahara

SPDX-License-Identifier: AGPL-3.0-or-later
-->

# Setting up FreeScout

This is an [Ansible](https://www.ansible.com/) role which installs [FreeScout](https://freescout.net) to run as a [Docker](https://www.docker.com/) container wrapped in a systemd service.

FreeScout is a free open-source helpdesk and shared inbox solution.

See the project's [documentation](https://github.com/freescout-help-desk/freescout/wiki) to learn what FreeScout does and why it might be useful to you.

## Prerequisites

To run an FreeScout instance it is necessary to prepare a database. You can use a [MySQL](https://www.mysql.com/) compatible database server or [Postgres](https://www.postgresql.org/).

If you are looking for Ansible roles for a MySQL compatible server or Postgres, you can check out [ansible-role-mariadb](https://github.com/mother-of-all-self-hosting/ansible-role-mariadb) and [ansible-role-postgres](https://github.com/mother-of-all-self-hosting/ansible-role-postgres), both of which are maintained by the [Mother-of-All-Self-Hosting (MASH)](https://github.com/mother-of-all-self-hosting) team.

## Adjusting the playbook configuration

To enable FreeScout with this role, add the following configuration to your `vars.yml` file.

**Note**: the path should be something like `inventory/host_vars/mash.example.com/vars.yml` if you use the [MASH Ansible playbook](https://github.com/mother-of-all-self-hosting/mash-playbook).

```yaml
########################################################################
#                                                                      #
# freescout                                                            #
#                                                                      #
########################################################################

freescout_enabled: true

########################################################################
#                                                                      #
# /freescout                                                           #
#                                                                      #
########################################################################
```

### Set the hostname

To enable FreeScout you need to set the hostname as well. To do so, add the following configuration to your `vars.yml` file. Make sure to replace `example.com` with your own value.

```yaml
freescout_hostname: "example.com"
```

After adjusting the hostname, make sure to adjust your DNS records to point the domain to your server.

#### Specify database

It is also necessary to select database used by FreeScout from a MySQL compatible database and Postgres.

To use Postgres, add the following configuration to your `vars.yml` file:

```yaml
freescout_database_type: postgres
```

Set `mysql` to use a MySQL compatible database.

For other settings, check variables such as `freescout_database_*` on [`defaults/main.yml`](../defaults/main.yml).

### Specify administrator's log in credentials

You also need to set the email address and password for the administrator by adding the following configuration to your `vars.yml` file:

```yaml
freescout_environment_variables_admin_email: ADMIN_EMAIL_ADDRESS_HERE
freescout_environment_variables_admin_password: ADMIN_PASSWORD_HERE
```

### Extending the configuration

There are some additional things you may wish to configure about the service.

Take a look at:

- [`defaults/main.yml`](../defaults/main.yml) for some variables that you can customize via your `vars.yml` file. You can override settings (even those that don't have dedicated playbook variables) using the `freescout_environment_variables_additional_variables` variable

See [the official documentation](https://freescout.apache.org/docs/env/) for a complete list of FreeScout's config options that you could put in `freescout_environment_variables_additional_variables`.

## Installing

After configuring the playbook, run the installation command of your playbook as below:

```sh
ansible-playbook -i inventory/hosts setup.yml --tags=setup-all,start
```

If you use the MASH playbook, the shortcut commands with the [`just` program](https://github.com/mother-of-all-self-hosting/mash-playbook/blob/main/docs/just.md) are also available: `just install-all` or `just setup-all`

## Usage

After running the command for installation, FreeScout becomes available at the specified hostname like `https://example.com`.

To get started, open the URL with a web browser to log in to the instance.

## Troubleshooting

FAQ is available on [this page](https://github.com/freescout-help-desk/freescout/wiki/FAQ).

### Check the service's logs

You can find the logs in [systemd-journald](https://www.freedesktop.org/software/systemd/man/systemd-journald.service.html) by logging in to the server with SSH and running `journalctl -fu freescout` (or how you/your playbook named the service, e.g. `mash-freescout`).
