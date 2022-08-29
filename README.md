# Base de Datos II

### Pre requirements

1. Open a Powershell terminal in administrator mode.

2. Intall Chocolatey (https://chocolatey.org/install)

```
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
```

3. Install Windows components (https://docs.microsoft.com/en-us/windows/wsl/install-manual)
```
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

4. Restart the machine.

5. Download and install WSL kernel update from https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi

6. Set WSL version
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

* Install Powershell 7 
```powershell WinGet
winget install Microsoft.Powershell
```
```powershell Chocolatey
choco install microsoft-powershell
```
* Install Windows Terminal with WinGet
```
winget install Microsoft.WindowsTerminal
```
* Install Windows Terminal with Chocolatey
```
choco install microsoft-windows-terminal
```
<details><summary>Winget</summary>
```
winget install Microsoft.Powershell
```
</details>
<details><summary>Chocolatey</summary>
```
choco install microsoft-powershell
```
</details>
