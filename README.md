#### reproduce issue
git clone https://github.com/keks24/ansible-issue-28772<br>
cd ./ansible-issue-28772<br>
vagrant up<br>

when vms are already set up, use:<br>
vagrant provision<br>
or<br>
vagrant up --provision<br>

to check user passwords:<br>
vagrant ssh provision-vm
cd credentials

#### link
https://github.com/ansible/ansible/issues/28772
