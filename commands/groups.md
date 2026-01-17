In linux every file or dir has 3 permission layers.
owner | group | others
ex.
owner (jeel) → read + write
group (devteam) → read only
oReal-world analogy

Think of a company:
Users → employees
Groups → teams (dev, ops, ml, finance)
Files → shared resources
Instead of:
“Give Alice access, give Bob access, give Charlie access…”
You say:
“Anyone in the ml-team can access this folder.”
That’s the entire purpose of groups.thers → no access

list all the groups in the system
cat /etc/group

create own group
sudo groupadd (group_name)

now append any user to that group
sudo usermod -aG (group_name) (user_name)
here -a mean append and -G means secondary group

then apply changes with
newgrp (group_name)

remove user from the group
sudo gpasswd -d (user_name) (group_name)

destroy the group
sudo groupdel (group_name)

change group ownership of a file or dir
sudo chgrp (group_name) (file or dir name)
for directory add -R for recursive permission inside that dir
sudo chgrp -R (group_name) (dir)

now also want to change owner and group then
sudo chown (owner):(group) (file or dir)
