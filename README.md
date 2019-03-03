# Ubuntu Postfix Setup

## Software

```
ansible-playbook playbooks/software.yml --extra-var "target=cryptoprocessing"
```

## Configs

### Run All Configs Setup

```
ansible-playbook playbooks/configs.yml --extra-var "target=cryptoprocessing"
```

### Available Tasks

```
ansible-playbook playbooks/configs_postfix.yml --extra-var "target=cryptoprocessing"
ansible-playbook playbooks/configs_opendkim.yml --extra-var "target=cryptoprocessing"
```
