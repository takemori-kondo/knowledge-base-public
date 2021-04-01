# eco-03. SSH
________________________________________
## 1. Open SSH
________________________________________
Win32-OpenSSH

```text
https://github.com/PowerShell/Win32-OpenSSH/releases
```

.ssh/config

```ini
# Open SSH
# OK : "/", "\"
# NG : Zenkaku
Host <Alias>
  HostName <Actual host>
  User <User>
  Port 22
  IdentityFile <private key file path>
```

for Vagrant via OpenSSH 

```shell
vagrant ssh
vagrant ssh-config
```

for SourceTree via OpenSSH

```text
SourceTree > tools > options > ssh client settings
    ssh key : If you need password for key, you need to write key path here.
    ssh client : OpenSSH

Clone source : git@<Alias>:<project owner>/<repository>.git (for gitlab)
```
