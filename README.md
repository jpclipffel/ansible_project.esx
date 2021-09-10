# ESX

ESX management via Ansible/AWX.

## Inventories

Project's inventories are located in the `inventories` sub-directory.

| Inventory | Description                                                |
|-----------|------------------------------------------------------------|
| `test`    | ICT lab ESX cluster                                        |
| `tenable` | Retrieve the ESX hosts from Tenable discovery scan results |


The inventories can be sourced by AWX from this project.

## Playbooks

Project's playbooks are located in the `playbooks` sub-directory.

| Playbook           | Description                                              | Ready for AWX |
|--------------------|----------------------------------------------------------|---------------|
| `ping.yml`         | Test connection and authentication to ESX hosts          | Yes           |
| `tenable_user.yml` | Manage the Tenable (Nessus) service account on ESX hosts | Yes           |

Most playbooks requires an access to PMP to get the ESX node password:

* If you using Ansible in command line, you may configure PMP access by setting
  the following environement variables
* If you are using AWX, you may add a custom credential template with the
  following environment variables

| Variable        | Description                          | Example                                |
|-----------------|--------------------------------------|----------------------------------------|
| `PMP_URL`       | PMP URL without the trailing `/`     | `https://pmp.dt.ept.lu`                |
| `PMP_AUTHTOKEN` | PMP authentication token for Ansible | `00000000-0000-0000-0000-000000000000` |


### Playbook - `test.yml`

This playbook test the connection to the ESX hosts.
If run with `--tags debug`, some information regarding the host users will be
displayed.

Example usage:

```shell
ansible-playbook -i inventories/test playbooks/test.yml --tags debug
```

### Playbook - `tenable_user.yml`

This playbook configure or deconfigure a service account on each ESX host for
Tenable.

#### Tags

| Tag        | Description                                             |
|------------|---------------------------------------------------------|
| `setup`    | Create and configure the Tenable account                |
| `teardown` | Remove Tenable account configuration (but keep account) |
| `remove`   | Remove Tenable account configuration and account        |

#### Variables

| Variable                | Description              | Required with tags            |
|-------------------------|--------------------------|-------------------------------|
| `tenable_user_name`     | Tenable account name     | `setup`, `teardown`, `remove` |
| `tenable_user_password` | Tenable account password | `P4ssW0rd!!R3QuirmEnts`       |

#### Examples

##### Create account

```shell
ansible-playbook \
    -i inventories/test \
    playbooks/tenable_user.yml \
    --tags setup \
    -e tenable_user_name='svc_tenable' \
    -e tenable_user_password='P4ssW0rd!!R3QuirmEnts'
```

##### Remove account

```shell
ansible-playbook \
    -i inventories/test \
    playbooks/tenable_user.yml \
    --tags remove \
    -e tenable_user_name='svc_tenable' \
```
