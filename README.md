# WSL dev environment setup & instalation

## Setup steps

### 1. Enable the optional WSL feature from Powershell:
```
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
```

It can also be done from Settings > Updates & Security. Google it.

For WSL2 there's another command. Google it.

### 2. Install WSL - enable first in PowerShell then install Ubuntu from Microsoft Store
```
user: username
pass: *********
```

### 3. Install VSCode and extension `Remote - WSL`. Also install your preffered extensions for dev

Here are some examples:
- DotENV
- Docker
- vscode-icons
- Diff
- Git Blame
- Git History
- KillTasksButton
- Move TS - Move TypeScript files and update relative imports
- npm
- TSLint
- Visual Studio IntelliCode
- vscode-styled-components

### 4. In WSL:

1. Create `wsl.conf` in `/etc/` if it doesn't exists. Else, modify to contain this part (For uid and gid check: cat /etc/passwd):

    `user@host:~$ touch /etc/wsl.conf`
    ```
    [automount]
    enabled = true
    root = /
    options = "metadata,uid=1000,gid=1000,dmask=0022,umask=0022,fmask=0011"
    ```

2. Add to `~/.profile` (at the end)

    `user@host:~$ nano ~/.profile`
    ```
    # Note: Bash on Windows does not currently apply umask properly.
    if [[ "$(umask)" = "0000" ]]; then
    umask 0022
    fi
    ```

### 5. In Windows create folder `workspace` in C:\

### 6. Restart WSL (by restarting the computer or the wsl service: https://superuser.com/questions/1319478/rebooting-wsl-in-windows-1803)
Now, C:\ drive is mounted in /c/ instead of /mnt/c/ which is better for Docker

### 7. Update existing packages

`user@host:~$ sudo apt update`

`user@host:~$ sudo apt upgrade`

### 8. Open VSCode

`user@host:~$ code .`

This will install VSCode Server:

    Installing VS Code Server for x64 (5763d909d5f12fe19f215cbfdd29a91c0fa9208a)
    Downloading: 100%
    Unpacking: 100%
    Unpacked 2321 files and folders to /home/user/.vscode-server/bin/5763d909d5f12fe19f215cbfdd29a91c0fa9208a.

Check extensions in VSCode: Some must be installed also in WSL. They appear with Install in WSL: Ubuntu-20.04

### 9. Install nvm and node

`user@host:~$ curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.3/install.sh | bash`

`user@host:~$ nvm ls-remote`

`user@host:~$ nvm install --lts=dubnium`

### 10. Create symlink for ssh keys `~/.ssh -> /c/Users/<user>/.ssh`

`user@host:~$ ln -s /c/Users/<user>/.ssh ~`

IF you get this error when trying to clone for example `user@host:/c/workspace$ git clone git@domain.com:path/to/repository.git repo-name`:
```
Cloning into 'repo-name'...
Warning: Permanently added the ECDSA host key for IP address '**.***.**.***' to the list of known hosts.
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@         WARNING: UNPROTECTED PRIVATE KEY FILE!          @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
Permissions 0744 for '/home/user/.ssh/id_rsa' are too open.
It is required that your private key files are NOT accessible by others.
This private key will be ignored.
Load key "/home/user/.ssh/id_rsa": bad permissions
git@domain.com: Permission denied (publickey,keyboard-interactive).
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
```

then:

`user@host:~$ cd ~/.ssh`

`user@host:~/.ssh$ chmod 600 *`

### 12. Create symlink for .gitconfig

`user@host:~$ ln -s /c/Users/<user>/.gitconfig ~`

```
[user]
	email = user.email@domain.com
	name = User Name
[alias]
	up = "!git remote update -p; git merge --ff-only @{u}"
	upall = "!git branch|cut -c2-|while read B; do git branch --set-upstream-to=\"origin/${B}\" \"${B}\" && git checkout \"$B\" && git up; done"
```

### 11. Disable annoying bell beep in terminal / vim / less

https://stackoverflow.com/questions/36724209/disable-beep-of-linux-bash-on-windows-10

1. Uncomment `set bell-style none` and `set bell-style visible` in `/etc/inputrc`

	`user@host:~$ sudo nano /etc/inputrc`

2. Add this line to `~/.vimrc` (create `~/.vimrc` if not exists)

	`set visualbell`

3. To disable the beep also in less (i.e. also in man pages and when using "git diff") you need to add export LESS="$LESS -R -Q" in your ~/.profile file.

4. Add to `~/.bashrc` (at the end)

    `user@host:~$ nano ~/.bashrc`
    ```
    # Disable annoying bell sound in terminal
    setterm blength 0
    ```

## TROUBLESHOOT:

### 1. VSCode opened from inside a symbolic link (symlink) will encounter problems with git real time refresh

https://github.com/microsoft/vscode-remote-release/issues/314

Therefore, creating a symlink for /c/workspace inside the home folder is NOT recomended (like this `user@host:~$ ln -s /c/workspace ~`)

### 2. FOR WHEN IT WILL BREAK:

https://superuser.com/questions/1319478/rebooting-wsl-in-windows-1803
