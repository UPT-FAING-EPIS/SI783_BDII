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

### Optionals {.tabset}

#### tab 1
#### tab 2
* Install Powershell 7 
```javascript WinGet
winget install Microsoft.Powershell
```
```javascript Chocolatey
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
<!-- Tab links -->
<div class="tab">
  <button class="tablinks" onclick="openCity(event, 'London')">London</button>
  <button class="tablinks" onclick="openCity(event, 'Paris')">Paris</button>
  <button class="tablinks" onclick="openCity(event, 'Tokyo')">Tokyo</button>
</div>

<!-- Tab content -->
<div id="London" class="tabcontent">
  <h3>London</h3>
  <p>London is the capital city of England.</p>
</div>

<div id="Paris" class="tabcontent">
  <h3>Paris</h3>
  <p>Paris is the capital of France.</p>
</div>

<div id="Tokyo" class="tabcontent">
  <h3>Tokyo</h3>
  <p>Tokyo is the capital of Japan.</p>
</div>
