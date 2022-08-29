# Base de Datos II

Welcome to Base de Datos II course, this is a little tools guide that you use in this course.
This guide suposes that you are working in a Microsoft Windows 10 or superior machine given for the university in their labs.
This tools are multiplatform so you can install in others machine platforms like Linux or Mac if you need. 

1. [Requirement Tools](                   <#requirement-tools>)
   - [Docker Desktop](                    <#docker-desktop>)
   - [Azure Data Studio](                 <#azure-data-studio>)
1. [Optional Tools](                      <#optional-tools>)
   - [Chocolatey](                        <#chocolatey>)
   - [Powershell 7](                      <#powershell-7>)
   - [Windows Terminal](                  <#windows-terminal>)
---
## Requirement Tools

### Docker Desktop
1. Open a Powershell terminal in administrator mode.
1. Install Windows components (https://docs.microsoft.com/en-us/windows/wsl/install-manual)
   ```
   dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
   dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
   ```
1. Restart the machine.
1. Download and install WSL kernel update from https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi
1. Open again Powershell terminal in administrator mode and set WSL version
   ```
   wsl --set-default-version 2
   ```
1. Install ubuntu with choco
   <details><summary>WinGet</summary>

   ```
   winget install Canonical.Ubuntu.2204
   ```
   </details>
   <details><summary>Chocolatey</summary>

   ```
   choco install wsl-ubuntu-2204
   ```
   </details>

   > Or download and install from https://aka.ms/wslubuntu2204

1. Install Docker Desktop
   <details><summary>WinGet</summary>

   ```
   winget install Docker.DockerDesktop
   ```
   </details>
   <details><summary>Chocolatey</summary>

   ```
   choco install docker-desktop
   ```
   </details>

### Azure Data Studio
1. Open a Powershell terminal in administrator mode and run the following command. 
   <details><summary>WinGet</summary>

   ```
   winget install Microsoft.AzureDataStudio
   ```
   </details>
   <details><summary>Chocolatey</summary>

   ```
   choco install azure-data-studio
   ```
   </details>
   
   > Or download and install from [https://docs.microsoft.com/en-us/sql/azure-data-studio/download-azure-data-studio?view=sql-server-ver16]
   
---
## Optional Tools

### Chocolatey

1. Open a Powershell terminal in administrator mode and run the following command. (https://chocolatey.org/install)
```
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
```
### Powershell 7

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
1. Close this powershell and open a Powershell 7 terminal in administrator mode and run the following command.
   ```
   Install-Module PSReadLine -Force
   ```
1. Edit powershell profile file.
   ```
   notepad $PROFILE
   ```
1. Add the following text to set command history search.
   ```
   Import-Module PSReadLine
   Set-PSReadLineOption -PredictionSource History
   Set-PSReadLineOption -PredictionViewStyle ListView
   Set-PSReadLineOption -EditMode Windows
   ```
1. Save changes and close editor.
1. Finally, close and reopen Powershell 7.
### Windows Terminal

1. Open a Powershell terminal in administrator mode and run the following command.
   <details><summary>WinGet</summary>

   ```
   winget install Microsoft.WindowsTerminal
   ```
   </details>
   <details><summary>Chocolatey</summary>

   ```
   choco install microsoft-windows-terminal
   ```
   </details>

