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
| program_files_64 | `string` | | C:\Program Files | |
| program_files_32 | `string` | | C:\Program Files (x86) | |
| hiscout_tmp | `string` | | C:\tmp | |
| hiscout_version | `string` | | '3.9.5' | |
| hiscout_service_name | `string` | | 'HiScoutServices' | |
| hiscout_service_displayname | `string` | |  HiScout Services | |
| hiscout_exe_filename | `string` | | HiScout GRC Suite {{ hiscout_version }} | |
| hiscout_exe_path | `string` | | '{{ hiscout_tmp }}\{{ hiscout_exe_filename }}.exe' | |
| hiscout_program_path | `string` | | '{{ program_files_32 }}\HiScout GRC Suite' | |
| hiscout_data_filename | `string` | | HiScoutData | |
| hiscout_data_path: C:\{{ hiscout_data_filename }} | |
| hiscout_tech_user | `string` | | TechUser | |
| hiscout_tech_user_password | `string` | | "" | |
| hiscout_web_targetdir | `string` | | C:\inetpub\wwwroot | |
| hiscout_web_name | `string` | | HiScoutWeb | |
| hiscout_webadmin_name | `string` | | WebAdmin | |
| task_engine_name | `string` | |  TaskEngine | |
| hiscout_database_backup_name | `string` | | HiScoutMaster_3.3.0_BCM_ISM_DS | |
| hiscout_database_backup_path | `string` | | '{{ hiscout_tmp }}\{{ hiscout_database_backup_name }}' | |
| hiscout_pdf_filter_path | `string` | | '{{ program_files_64 }}\Adobe\Adobe PDF iFilter 11 for 64-bit platforms' | |
| hiscout_pdf_filter | `string` | | PDFFilter64Setup | |
| hiscout_log | `string` | | HiScoutService.InstallLog | |
| hiscout_domain | `string` | |  "" | |
| hiscout_cert_path | `string` | | '{{ hiscout_tmp }}\{{ hiscout_domain }}.p12' | |
| hiscout_cert_password | `string` | | "" | |
| mssql_instance_name | `string` | | MSSQL | |
| mssql_database_name | `string` | | HiScout_GRCMaster_330 | |
| mssql_database_relocate_name | `string` | | "HiScoutMaster_3.2.0" | |
| mssql_port | `string` | | '1433' | |
| mssql_login_user | `string` | | '{{ ansible_hostname | upper }}\{{ hiscout_tech_user }}' | |
| mssql_login_password | `string` | | '{{ hiscout_tech_user_password }}' | |
| mssql_host | `string` | | '{{ ansible_hostname }}\{{ mssql_instance_name }}' | |
| mssql_path | `string` | | C:\SQLDB | |
| hiscout_suppress_reboot | `string` | | false | |
| iis_site_name | `string` | | HiScout | |

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
