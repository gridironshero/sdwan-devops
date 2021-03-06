## Operating the Topology

### Requirements

* A license file located at `licenses\viptela_serial_file.viptela`
* The organization name associated with the license file.

## Configure the Topology

>Note: You'll need to create the `licenses` directory in the `ps-crn` directory
### Step 1

The easiest way to specify the organization name is to modify `organization_name` in `inventory/group_vars/all/viptela.yml`:

```shell
organization_name: ""
```

Now run `configure.yml` running only those plays with the tag `control`:

```shell
ansible-playbook configure.yml --tags=control
```

Alternatively, organization name can be passed in as an extra var (**note commas**): 

```shell
ansible-playbook configure.yml -e 'organization_name="<your org name>"' --tags=control
```

`configure.yml` can also be run with the following tags:
* `CA`: only create the private CA
* `control`: only provision the Viptela control plane
* `vedge`: only provision the Viptela vEdges
* `check_control`: check connectivity of the control plane
* `check_edge`: check connectivity of the edge
* `check_all`: check full connectivity of the overlay

This playbook will:
* Check for the existance of the license file
* Check initial connectivity
* Generate a local CA
* Configure all Viptela elements
* Create CSR, sign CSR, and install certificate
* Add vbond and vsmart to vmanage

### Step 2: Configure and provision the vedges:

```shell
ansible-playbook configure.yml -e 'organization_name="<your org name>"' --tags=edge
```

#### Wait for the vEdges to sync:

```shell
ansible-playbook waitfor-sync.yml
```

### Step 3: Import templates

```shell
ansible-playbook import-templates.yml
```
#### Extra Vars
* `vmanage_ip`

To specify the IP address of the vManage server into which to import the templates:
```yaml
ansible-playbook import-templates.yml -e vmanage_ip=1.2.3.4
```

#### Attach templates to devices
```yaml
ansible-playbook attach_templates.yml
```

To attach a template to a limited set of devices:
```yaml
ansible-playbook attach_templates.yml --limit=east-rtr1,west-rtr1
```

### Step 4: Import Policy

```shell
ansible-playbook import-policy.yml
```
#### Extra Vars
* `vmanage_ip`

To specify the IP address of the vManage server into which to import the templates:
```yaml
ansible-playbook import-policy.yml -e vmanage_ip=1.2.3.4
```

#### Activate Central Policy
```yaml
ansible-playbook activate-central-policy.yml
```


### Export templates
```yaml
ansible-playbook export-templates.yml
```

#### Extra Vars
* `vmanage_ip`

To specify the IP address of the vManage server from which to export the templates:
```yaml
ansible-playbook export-temapltes.yml -e vmanage_ip=1.2.3.4
```

### Detach templates from devices
```yaml
ansible-playbook detach_templates.yml
```