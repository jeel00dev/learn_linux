# Linux Groups and Permissions

## Permission Layers

In Linux, every file or directory has three permission layers:

- **Owner**: The user who owns the file or directory.
- **Group**: The group associated with the file or directory.
- **Others**: All other users on the system.

### Example

- Owner (e.g., jeel) → read + write
- Group (e.g., devteam) → read only
- Others → no access

## Real-World Analogy

Think of a company:

- **Users** → Employees
- **Groups** → Teams (e.g., dev, ops, ml, finance)
- **Files** → Shared resources

Instead of granting access individually (e.g., "Give Alice access, give Bob access, give Charlie access..."), you can grant access to the entire group (e.g., "Anyone in the ml-team can access this folder.").

That's the primary purpose of groups.

## Managing Groups

### List All Groups in the System

```bash
cat /etc/group
```

### Create a Group

```bash
sudo groupadd <group_name>
```

### Add a User to a Group

```bash
sudo usermod -aG <group_name> <user_name>
```

- `-a`: Append (add without removing existing groups).
- `-G`: Secondary group.

### Apply Changes

```bash
newgrp <group_name>
```

### Remove a User from a Group

```bash
sudo gpasswd -d <user_name> <group_name>
```

### Delete a Group

```bash
sudo groupdel <group_name>
```

### Change Group Ownership of a File or Directory

```bash
sudo chgrp <group_name> <file_or_dir_name>
```

For directories, add `-R` for recursive changes:

```bash
sudo chgrp -R <group_name> <dir_name>
```

### Change Owner and Group

```bash
sudo chown <owner>:<group> <file_or_dir_name>
```
