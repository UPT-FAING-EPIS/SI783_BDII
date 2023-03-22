# Base de Datos II

Welcome to Base de Datos II course, this is a little tools guide that you use in this course.
This guide suposes that you are working in a Microsoft Windows 10 or superior machine given for the university in their labs.
This tools are multiplatform so you can install in others machine platforms like Linux or Mac if you need. 

1. [Requirement Tools](                   <#requirement-tools>)
   - [Docker Desktop](                    <#docker-desktop>)
   - [Git](                               <#git>)
   - [Visual Studio Code](                <#visual-studio-code>)
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
1. Download WSL kernel update from https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi
   ```
   curl -o wsl_update_x64.msi https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi
   ```
1. Restart the machine.
   ```
   shutdown /r /t 0
   ```
1. Open again a Powershell terminal in administrator mode and 
   ```
   .\wsl_update_x64.msi /quiet
   ```
1. Set WSL version
   ```
   wsl --set-default-version 2
   ```
1. Install ubuntu Subsytem
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
  
1. Verify ubuntu Subsytem installed and use versión 2
   ```
   wsl -l -v
   ```
   This show something like this:
   
   NAME            STATE           VERSION
   * Ubuntu-22.04    Stopped         2
   
   if not, start ubuntu subsytem in Windows and make necessary configurations, after re run prior command.
   
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

1. logoff the machine.
   ```
   logoff
   ```

### Git
1. Open a Powershell terminal in administrator mode and run the following command. 
   <details><summary>WinGet</summary>

   ```
   winget install Git.Git
   ```
   </details>
   <details><summary>Chocolatey</summary>

   ```
   choco install git-scm
   ```
   </details>
   
   > Or download and install from [https://git-scm.com/downloads]


### Visual Studio Code
1. Open a Powershell terminal in administrator mode and run the following command. 
   <details><summary>WinGet</summary>

   ```
   winget install Microsoft.VisualStudioCode
   ```
   </details>
   <details><summary>Chocolatey</summary>

   ```
   choco install Microsoft.VisualStudioCode
   ```
   </details>
   
   > Or download and install from [https://code.visualstudio.com/download]


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


### Net 6
1. Open a Powershell terminal in administrator mode and run the following command. 
   <details><summary>WinGet</summary>

   ```
   winget install Microsoft.DotNet.SDK.6
   ```
   </details>
   <details><summary>Chocolatey</summary>

   ```
   choco install Microsoft.DotNet.SDK.6
   ```
   </details>
   
   > Or download and install from [https://docs.microsoft.com/en-us/sql/azure-data-studio/download-azure-data-studio?view=sql-server-ver16]


### Python
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

