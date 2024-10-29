# This playbook downloads a file
> Replace the URL with the required url
> make a vars file and keep the credentials secured there by using ansible vaults
> replace the destination path as per your requirement

```
---
- name: Download a file to a server
  hosts: all
  become: yes
  gather_facts: false
  vars_files:
    - /var/test_secretkeys.yml

  tasks:
    - name: Download the file
      win_get_url:
        url: "ente the file url"
        dest: "C:\path"
        force: yes
        become_user: "{{ ansible_username }}"
        become_password: "{{ ansible_password }}"
```
