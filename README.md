#### reproduce issue
```bash
git clone https://github.com/keks24/ansible-issue-28772
cd ./ansible-issue-28772
vagrant up
```

to check user passwords in provision-vm:<br>
```bash
vagrant ssh provision-vm
cd credentials
```

to re-create development-vm:
```bash
vagrant destroy development-vm
vagrant provision
```

#### link
https://github.com/ansible/ansible/issues/28772
