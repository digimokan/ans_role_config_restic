# ans_role_config_restic

Install the restic backup program, and configure backups to a remote repo.

[![Release](https://img.shields.io/github/release/digimokan/ans_role_config_restic.svg?label=release)](https://github.com/digimokan/ans_role_config_restic/releases/latest "Latest Release Notes")
[![License](https://img.shields.io/badge/license-MIT-blue.svg?label=license)](LICENSE.md "Project License")

## Table Of Contents

* [Purpose](#purpose)
* [Background](#background)
* [Supported Operating Systems](#supported-operating-systems)
* [Quick Start](#quick-start)
    * [Use From Playbook](#use-from-playbook)
* [Role Options](#role-options)
* [Contributing](#contributing)

## Purpose

* Install the [restic](https://restic.readthedocs.io/en/latest/index.html)
  backup program.
* Configure remote backups.
* Optionally configure automatic periodic remote backups.
* Simplify restic usage by providing a [utility script](../templates/do_restic.j2).

## Background

* restic can back up files to a variety of remote servers and protocols.
* This role configures backups to a compatible remote
  [AWS S3 bucket](https://restic.readthedocs.io/en/latest/030_preparing_a_new_repo.html#amazon-s3).
* The remote AWS S3 bucket holds a _restic repo_, which must be initialized.
* The remote repo is accessed via these credentials:
      * `AWS_ACCESS_KEY_ID` (issued by bucket provider)
      * `AWS_SECRET_ACCESS_KEY` (issued by bucket provider)
      * repo password (set by user)
* Multiple machines can send backups to the repo.
* The remote can only host one repo.
* _WARNING: ensure that all restic operations are executed by the same user._

## Supported Operating Systems

* Arch Linux
* FreeBSD

## Quick Start

### Use From Playbook

1. Create `requirements.yml` in ansible project root, and add this content:

   ```yaml
   # requirements.yml
   - src: https://github.com/digimokan/ans_role_config_restic
   ```

2. From the project root directory, install/download the role:

   ```shell
   $ ansible-galaxy install --role-file requirements.yml --roles-path ./roles --force-with-deps
   ```

   * _NOTE:_ `--force-with-deps` _ensures subsequent calls download updates_

3. Include the role like any local role, from the project playbook:

   ```yaml
   # playbook.yml
   - hosts: localhost
     connection: local
     tasks:
       - name: "Install the restic backup program, and configure backups to a remote repo"
         ansible.builtin.include_role:
           name: ans_role_config_restic
         vars:
           restic_user_name: 'user2'
           restic_aws_bucket_url: 's3:https://s3.wasabisys.com/my-bucket-name'
           restic_aws_access_key_id: '<MY-WASABI-ACCESS-KEY-ID>'
           restic_aws_secret_access_key: '<MY-WASABI-SECRET-ACCESS-KEY>'
           enable_automatic_backups: true
           automatic_backup_dirs:
             - '/home/user2/Documents/'

   ```

## Role Options

See the role `defaults` files for main role vars listings:

  * [defaults](../defaults/main/)

Define these _required_ vars for the role:

  * `restic_user_name`: name of primary restic user to configure the backups for
  * `restic_aws_bucket_url`: an AWS S3 bucket that will host the remote repo
  * `restic_aws_access_key_id`: the provider-issued ID for the bucket
  * `restic_aws_secret_access_key`: the provider-issued key-string for the bucket

## Contributing

* Feel free to report a bug or propose a feature by opening a new
  [Issue](https://github.com/digimokan/ans_role_config_restic/issues).
* Follow the project's [Contributing](CONTRIBUTING.md) guidelines.
* Respect the project's [Code Of Conduct](CODE_OF_CONDUCT.md).

