# Install-Sysmon
Powershell Script to Install Sysmon as service with rules from SwiftonSecurity

.SYNOPSIS

Install-Sysmon downloads the Sysmon executables archive and installs Sysmon64.exe
with a configuration file.

.DESCRIPTION

PowerShell script or module to install Sysmon with configuration 

.PARAMETER path

The path to the working directory.  Default is user Documents.

.EXAMPLE

Install-Sysmon -path C:\Users\example\Desktop
