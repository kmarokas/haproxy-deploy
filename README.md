# deployment-haproxy

## Deployment über Tags


```yaml
- name: Deploy HAProxy - tenant 'acme'
  hosts: acme
  tags: [acme]
  vars:
    haproxy_miw_tenant_folders: [acme]
  tasks:
    - ansible.builtin.import_tasks: tasks/deploy_tenant_instances.yml
```

```bash
ansible-playbook -i inventories/production playbooks/deploy_haproxy.yml --tags acme
```


### Partial Deploys

```bash
ansible-playbook -i inventories/production playbooks/deploy_haproxy.yml --tags acme \
  -e '{"haproxy_miw_tenant_folders": [], "haproxy_miw_instances": [{"tenant": "acme", "instance": "web01"}]}'
```

### Undeploy

Eine einzelne Instanz entfernen:

```bash
ansible-playbook -i inventories/production playbooks/undeploy_haproxy.yml \
  -e '{"haproxy_miw_undeploy_instances": [{"tenant": "acme", "instance": "web01"}]}'
```

Mehrere Instanzen in einem Lauf:

```bash
ansible-playbook -i inventories/production playbooks/undeploy_haproxy.yml \
  -e '{"haproxy_miw_undeploy_instances": [
        {"tenant": "acme", "instance": "web01"},
        {"tenant": "acme", "instance": "api01"}
      ]}'
```

Einen ganzen Tenant entfernen (löscht jedes `conf.d/<tenant>-*.cfg` + `<tenant>__shared.cfg`
auf jedem Host der Fleet, auf dem dieser Tenant tatsächlich liegt):

```bash
ansible-playbook -i inventories/local playbooks/undeploy_haproxy.yml --limit haproxy-local-web01 \
  -e '{"haproxy_miw_undeploy_tenants": ["tenant1"]}'
```


## `requirements.yml`

`ansible-galaxy collection install -r requirements.yml`
