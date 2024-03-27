# windows-10-enterprise-x64-eval vagrant box

This vagrantbox starts a windows 10 machine including

- ssh server
- adds public key (from a id_ed25519.pub file)
- add firewall rule for the sshd
- installs vim

## Usage

### Start

The first start might take a while...

    %> vagrant up
    # wait till machine is ready
    %> vagrant ssh

### Stop

    %> vagrant halt

### Cleanup

    %> vagrant destroy

## SSH Login

Default credentials are

    User:     vagrant
    Password: vagrant

Creating a `.ssh/config` like this:

    Host win10-enterprise-x64-eval
        HostName 127.0.0.1
        User administrator
        Port 2222
        UserKnownHostsFile /dev/null
        StrictHostKeyChecking no

allows to login by ssh.

## Customizing

More provisioning can be done by creating a `./provision.ps1` powershell script.

Example `provision.ps1`:

```powershell
# install golang
$ProgressPreference = 'SilentlyContinue'
Invoke-WebRequest -UseBasicParsing -Uri https://go.dev/dl/go1.22.1.windows-amd64.msi -OutFile go1.22.1.windows-amd64.msi
Install-Package -force ./go1.22.1.windows-amd64.msi
```
