```
---
- name: " This playbook is to apply patches to linux servers"
  hosts: node1,node2
  serial: 2
  tasks:
  - name: check application status
## ADD the lines of applications you want to check
    shell: systemctl is-active httpd
    register: application_process_check
    


  - name: "Applying patches to the server"
    yum: name=kernel state=latest
    when: application_process_check.stdout == "Active.\r\n" && ansible_distribution == "RedHat"
    register: patch_update
  - name: "Check if reboot required"
    shell: KERNEL_NEW=$(rpm -q --last kernel |head -1 | awk '{print $1}'|sed 's/kernel-//'); KERNEL_NOW=$(uname -r); if [[ $KERNEL_NEW != $KERNEL_NOW ]]; then echo "reboot needed"; else echo "reboot not needed"; fi
    ignore_errors: true
    register: reboot_status
  - name: "This play is to restart the system using above status check"
    command: shutdown -r +1 "Rebooting System After Patching"
    async: 0
    poll: 0
    when: reboot_status.stdout == "reboot needed"
    register: reboot_started
    ignore_errors: true

  - name: "This play will wait for 1 minutes for system to come up"
    pause: minutes=1
    
    # Now we will run a local 'ansible -m ping' on this host until it returns.
    # This works with the existing ansible hosts inventory and so any custom ansible_ssh_hosts definitions are being used
  - name: check the system status
    local_action: shell ansible -u ansible -m ping {{ inventory_hostname }}
    register: resultThe complete playbook, including all tasks, is shown above.
    until: result.rc == 0
    retries: 30
    delay: 10

    # And finally, execute 'uptime' when the host is back.
  - name: check the client uptime
    shell: uptime
```
