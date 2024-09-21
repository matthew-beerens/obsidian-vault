
# Footprinting

```
sudo nmap 10.129.14.128 -p111,2049 -sV -sC
```

```
sudo nmap --script nfs* 10.129.14.128 -sV -p111,2049
```

## Mounting NFS to our system

We can follow the procedure below to list the available NFS shares on the host, and then mount one to our local system:
```
showmount -e $RHOST
mkdir LOCAL_MOUNT_FOLDER_NAME
sudo mount -t nfs $RHOST:/ ./LOCAL_MOUNT_FOLDER_NAME -o nolock
cd LOCAL_MOUNT_FOLDER_NAME
tree .
```

Once mounted we will have the opportunity to access the rights and the usernames and groups for who the viewable and shown files belong to. Once we have this information, we can create them on our local system and adapt them to the NFS share to view and modify the files.

We can find this information by:
```
ls -l mnt/nfs/       # usernames and group names
ls -n mnt/nfs/       # UIDs and GUIDs
```

We can then unmount from our system when ready:
```
sudo umount ./<LOCAL_NFS_FOLDER>
```
# Basics

NFS is a network file system developed by sun microsystems. It serves the same purpose as SMB (access file systems over the network as if they were local).

However, NFS is a completely different protocol from SMB and is used between Linux and Unix systems.

|**Version**|**Features**|
|---|---|
|`NFSv2`|It is older but is supported by many systems and was initially operated entirely over UDP.|
|`NFSv3`|It has more features, including variable file size and better error reporting, but is not fully compatible with NFSv2 clients.|
|`NFSv4`|It includes Kerberos, works through firewalls and on the Internet, no longer requires portmappers, supports ACLs, applies state-based operations, and provides performance improvements and high security. It is also the first version to have a stateful protocol.|

NFS makes use of client information passed to the RPC protocol for authentication and follows type UID/GID and group membership information for access control.

So, NFS should Only be used on trusted secure networks, as once mounted we can replicate users, groups and permissions on our system to read, and modify files.
# Default Configuration

We can find the configuration which contains the table of physical filesystems on the NFS server accessible by clients at `/etc/exports` - it is here we also configure the shared on the server, including read write perms.

some options for configuration:

| **Option**         | **Description**                                                                                                                             |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------- |
| `rw`               | Read and write permissions.                                                                                                                 |
| `ro`               | Read only permissions.                                                                                                                      |
| `sync`             | Synchronous data transfer. (A bit slower)                                                                                                   |
| `async`            | Asynchronous data transfer. (A bit faster)                                                                                                  |
| `secure`           | Ports above 1024 will not be used.                                                                                                          |
| `insecure`         | Ports above 1024 will be used.                                                                                                              |
| `no_subtree_check` | This option disables the checking of subdirectory trees.                                                                                    |
| `root_squash`      | Assigns all permissions to files of root UID/GID 0 to the UID/GID of anonymous, which prevents `root` from accessing files on an NFS mount. |

## Adding item to NFS

we can add a fileshare to NFS by:
```
echo '/mnt/nfs <network_ID>/<MASK>(option1, ... option5)' >> /etc/exports
systemctl restart nfs-kernel-server
exportfs
```


## Dangerous Settings

|**Option**|**Description**|
|---|---|
|`rw`|Read and write permissions.|
|`insecure`|Ports above 1024 will be used.|
|`nohide`|If another file system was mounted below an exported directory, this directory is exported by its own exports entry.|
|`no_root_squash`|All files created by root are kept with the UID/GID 0.|

