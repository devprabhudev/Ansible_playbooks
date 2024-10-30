# Description: This playbook is for demonstrating the way ansible handlers work
# Author: Novatec IT Services
```
- hosts:
  remote_user: root
  become: yes
  tasks:
    - name: Install Apache
      yum: 
        name: httpd
        state: installed
      notify:
        - Restart Apache
    - name: Install MySql
      yum:
        name: mysql
        state: started
      notify:
        - Restart Apache
  handlers:
    - name: Restart Apache
      service:
        name: httpd
        state: restarted
```
