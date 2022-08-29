# Base de Datos II

1. [Requirement Tools](                   <#requirement-tools>)
   - [Docker](                            <#docker>)
3. [Optional Tools](                      <#optional-tools>)
   - [Chocolatey](                        <#chocolatey>)
   - [Powershell 7](                      <#powershell>)
   - [Windows Terminal](                  <#windowsterminal>)

## Requirement Tools

### Docker
1. Open a Powershell terminal in administrator mode.
1. Install Windows components (https://docs.microsoft.com/en-us/windows/wsl/install-manual)
```
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```
1. Restart the machine.
1. Download and install WSL kernel update from https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi
1. Set WSL version
```
wsl --set-default-version 2
```
7. Install ubuntu with choco
```
choco install wsl-ubuntu-2204
```
> Or download and install from https://aka.ms/wslubuntu2204

8. Install Docker Desktop with choco
```
choco install docker-desktop
```
---
## Optional Tools

### Chocolatey

1. Open a Powershell terminal in administrator mode and run the following command. (https://chocolatey.org/install)
```
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
```
### Chocolatey

1. Open a Powershell terminal in administrator mode and run the following command.
   <details><summary>WinGet</summary>

   ```
   winget install Microsoft.Powershell
   ```
   </details>
   <details><summary>Chocolatey</summary>

   ```
   choco install microsoft-powershell
   ```
   </details>
   
* Install Windows Terminal with WinGet
```
winget install Microsoft.WindowsTerminal
```
* Install Windows Terminal with Chocolatey
```
choco install microsoft-windows-terminal
```

