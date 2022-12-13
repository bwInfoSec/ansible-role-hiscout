# ansible-role-hiscout

Ansible Role for [HiScout](https://www.hiscout.com/)

## Getting Started

Clone this repository into your repository

```sh
git clone git@github.com:bwInfoSec/ansible-role-hiscout.git
```

# Role Variables

| Variable                     | Type    | Choices     | Default           | Comment                                                                       |
|------------------------------|---------|-------------|-------------------|-------------------------------------------------------------------------------|

## Example Playbook

```yaml
---
- name: Deploy Hiscout
  hosts: "hiscout-server"
  gather_facts: false
  roles:
    - hiscout
  pre_tasks:
    - name: Setup ssh-proxy connection
      delegate_to: 127.0.0.1
      ansible.builtin.command: "{{ ssh_proxy_setup_command }}"
      changed_when: false
      tags:
        - always

    - name: Gather facts
      ansible.builtin.setup:
      tags:
        - always

  post_tasks:
    - name: Get PID of ssh-proxy
      delegate_to: 127.0.0.1
      ansible.builtin.command: "{{ ssh_proxy_setup_command }} -O check"
      register: pid_ssh_proxy
      changed_when: false
      tags:
        - always

    - name: Terminate ssh-proxy
      delegate_to: 127.0.0.1
      ansible.builtin.command: kill -9 {{ pid_ssh_proxy | regex_search('[(]pid=[0-9]+[)]') | regex_search('[0-9]+') }}
      changed_when: false
      tags:
        - always
```
