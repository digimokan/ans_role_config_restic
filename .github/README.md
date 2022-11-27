# ans_role_config_restic

Install the restic backup program, and configure backups to a remote repo.

[![Release](https://img.shields.io/github/release/digimokan/ans_role_config_restic.svg?label=release)](https://github.com/digimokan/ans_role_config_restic/releases/latest "Latest Release Notes")
[![License](https://img.shields.io/badge/license-MIT-blue.svg?label=license)](LICENSE.md "Project License")

## Table Of Contents

* [Purpose](#purpose)
* [Background](#background)
* [Requirements](#requirements)
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
* This role configures backups to an AWS-S3-compatible remote
  [bucket](https://restic.readthedocs.io/en/latest/030_preparing_a_new_repo.html#amazon-s3).
* The remote S3 bucket holds a [restic repo](https://restic.readthedocs.io/en/latest/030_preparing_a_new_repo.html).
* The remote repo is accessed via these credentials:
      * [`RESTIC_REPOSITORY`](../defaults/main/remote_repo.yml)
      * [`AWS_ACCESS_KEY_ID`](../defaults/main/remote_repo.yml)
      * [`AWS_SECRET_ACCESS_KEY`](../defaults/main/remote_repo.yml)
      * [repo password keystring](../defaults/main/remote_repo.yml)
* On the remote, a repo can be [initialized only once](https://forum.restic.net/t/restoring-on-a-new-host/1182).
* Once initialized, the most you can do is [wipeout all snapshots](https://github.com/restic/restic/issues/1977#issuecomment-417393284).
* Initializing the repo takes a [keystring as input](https://restic.readthedocs.io/en/latest/030_preparing_a_new_repo.html#local).
* The keystring is used to create a key, with a [master-key subcomponent](https://forum.restic.net/t/restic-key-passwd-options/5425/4).
* The same machine can [create more keys](https://restic.readthedocs.io/en/latest/070_encryption.html#manage-repository-keys)
  (all with the same master-key subcomponent).
* Anyone with the S3 `KEY_ID` and `ACCESS_KEY` can access the remote, but only
  a key with the master-key subcomponent can
  [access the repo and its data](https://restic.readthedocs.io/en/latest/030_preparing_a_new_repo.html#local).
* Therefore, ensure that the keystring is backed up somewhere.
* Also, ensure that all restic operations are executed by the same user.

## Requirements

1. S3 bucket has been created on an S3-compatible storage provider.
2. Bucket has been initialized.
    * The [utility script](../templates/do_restic.j2) can be used to initialize
      the bucket.
    * It is ok if the bucket already has snapshots; if the same files are
      being backed up, they will be deduplicated.

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
           restic_s3_bucket_url: 's3:https://s3.someprovider.com/my-bucket-name'
           restic_s3_access_key_id: '<MY-S3-ACCESS-KEY-ID>'
           restic_s3_secret_access_key: '<MY-S3-SECRET-ACCESS-KEY>'
           enable_automatic_backups: true
           automatic_backup_dirs:
             - '/home/user2/Documents/'
   ```

## Role Options

See the role `defaults` files for main role vars listings:

  * [defaults](../defaults/main/)

Define these _required_ vars for the role:

  * `restic_user_name`: name of primary restic user to configure the backups for
  * [S3 access credentials and repo password](../defaults/main/remote_repo.yml)

## Contributing

* Feel free to report a bug or propose a feature by opening a new
  [Issue](https://github.com/digimokan/ans_role_config_restic/issues).
* Follow the project's [Contributing](CONTRIBUTING.md) guidelines.
* Respect the project's [Code Of Conduct](CODE_OF_CONDUCT.md).

