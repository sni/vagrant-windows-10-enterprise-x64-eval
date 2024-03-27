# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "peru/windows-10-enterprise-x64-eval"
  config.vm.box_check_update = true
  config.vm.network "private_network", ip: "192.168.56.10"

  config.vm.provider "virtualbox" do |vb|
     vb.gui    = true
     vb.memory = "2048"
     vb.customize ["storageattach", :id, "--storagectl", "IDE Controller", "--port", "0", "--device", "0", "--type", "dvddrive", "--medium", "emptydrive"]
   end

  ssh_key_path = "./id_ed25519.pub"
  ssh_key_data = File.read(ssh_key_path) if File.exist?(ssh_key_path)

  config.vm.provision "shell", privileged: true, inline: <<-SHELL
    # disable Invoke-WebRequest progress bar makes it way faster
    $ProgressPreference = 'SilentlyContinue'

    # enable debug output
    #Set-PSDebug -Trace 2

    # always show file extensions in explorer
    Set-ItemProperty -Path "HKCU:\\Software\\Microsoft\\Windows\\CurrentVersion\\Explorer\\Advanced" -Name "HideFileExt" -Value 0

    if (!(Get-NetFirewallRule -Name "OpenSSH-Server-In-TCP" -ErrorAction SilentlyContinue | Select-Object Name, Enabled)) {
      Write-Output "Firewall Rule 'OpenSSH-Server-In-TCP' does not exist, creating it..."
      Add-WindowsCapability -Online -Name OpenSSH.Server
      Start-Service sshd
      Set-Service -Name sshd -StartupType 'Automatic'
      New-NetFirewallRule -Name 'OpenSSH-Server-In-TCP' -DisplayName 'OpenSSH Server (sshd)' -Enabled True -Direction Inbound -Protocol TCP -Action Allow -LocalPort 22
    } else {
      Write-Output "Firewall rule 'OpenSSH-Server-In-TCP' already exists."
    }

    # add our ssh key
    $lineCount = (Get-Content -Path "C:/ProgramData/ssh/administrators_authorized_keys" | Measure-Object -Line).Lines
    if ($lineCount -eq 1 and "#{ssh_key_data}" -ne "") {
      Add-Content -Path "C:/ProgramData/ssh/administrators_authorized_keys" -Value "#{ssh_key_data}"
    }

    # install vim
    Invoke-WebRequest -UseBasicParsing -Uri https://github.com/vim/vim-win32-installer/releases/download/v9.1.0196/gvim_9.1.0196_x86.zip -OutFile gvim_9.1.0196_x86.zip
    Expand-Archive -LiteralPath ./gvim_9.1.0196_x86.zip -DestinationPath 'C:/Program Files'
  SHELL
  config.vm.provision "shell", privileged: true, path: "./provision.ps1" if File.exist?("./provision.ps1")
end
