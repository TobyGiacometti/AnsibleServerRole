# Ansible Server Role

An [Ansible][1] role that manages the foundation for a Linux server.

## Table of Contents

- [Features](#features)
- [Requirements](#requirements)
- [Installation](#installation)
- [Usage](#usage)
    - [Variables](#variables)
    - [Example](#example)

## Features

- Swap file management
- Automatic updates
- Clock management
- Locales management
- User management
- Hardening

## Requirements

- Debian GNU/Linux 10 (Buster) on managed host

## Installation

Use [Ansible Galaxy][2] to install `tobygiacometti.server`. Check out the Ansible Galaxy [content installation instructions][3] if you need help.

## Usage

To get general guidance on how to use Ansible roles, visit the [official documentation][4].

### Variables

- `server_swap_file_mb`: Size of the swap file in megabytes. If not specified, the swap file is not managed.
- `server_ssh_port`: Port that the SSH server should listen on. Defaults to 22.
- `server_update_email`: Address to which automatic package update report emails should be sent. A username can be provided to deliver report emails to a local user account.
- `server_update_success_report`: Boolean indicating whether report emails should be sent after each automatic package update. Defaults to `false` (report emails are only sent on failure).
- `server_update_reboot`: Boolean indicating whether the server should be rebooted (if required) after an automatic package update. Defaults to `true`.
- `server_time_zone`: Time zone for the system clock. A list of supported time zone definitions can be retrieved by executing `timedatectl list-timezones` on the server. If not specified, the time zone for the system clock is not managed.
- `server_locales`: List of system locales that should be generated. A list of supported locale definitions can be found in the file `/usr/share/i18n/SUPPORTED` on the server. If not specified, system locales are not managed.
- `server_users`: Users that can access the server through SSH. Please note that users are not automatically removed from the server when removed from this variable. This variable takes a list of dictionaries with following key-value pairs:
    - `name`: Name of the user.
    - `state`: State of the user, either `present` (default) or `absent`. Please note that the user's home directory is automatically removed.
    - `password`: Password for the user. If the special variable `omit` is provided and the user exists, the password is left unchanged. To disable password authentication, set to `false`.
    - `ssh_keys`: SSH authorized keys (one per line) for the user.
    - `sudo`: Whether the user should be allowed to use `sudo`.
    - `shell`: Login shell for the user. Defaults to `/bin/bash`.

### Example

```yaml
- hosts: servers
  vars_prompt:
    - name: example_user_password
      prompt: Password for example user
      unsafe: true
  roles:
    - role: tobygiacometti.server
      vars:
        server_swap_file_mb: 512
        server_update_email: user@domain.example
        server_time_zone: UTC
        server_locales:
          - en_US.UTF-8 UTF-8
        server_users:
          - name: example1
            password: "{% if example_user_password == '' %}{{ omit }}{% else %}{{ example_user_password }}{% endif %}"
            ssh_keys: |
              ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAILLvN9CeszM2AVXN71S6oykDvK/zmcS5M4v9fUAFLS7W
              ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIE2t3yUgO8zLwut+2asWzhcebhrnsKwUF4KBeB8SyhRS
            sudo: true
            
          - name: example2
            password: false
            ssh_keys: ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAILLvN9CeszM2AVXN71S6oykDvK/zmcS5M4v9fUAFLS7W
            sudo: false
```

[1]: https://www.ansible.com
[2]: https://galaxy.ansible.com
[3]: https://galaxy.ansible.com/docs/using/installing.html
[4]: https://docs.ansible.com/ansible/latest/user_guide/playbooks_reuse_roles.html
