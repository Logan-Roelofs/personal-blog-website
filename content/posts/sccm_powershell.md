---
title: "Create Custom Installers/Uninstallers Using Powershell For MEM/SCCM"
date: 2024-07-12
---

---

## The Basics

The following script is useful for creating custom installers and uninstallers in Powershell. The advantage to creating script installers versus uploading a regular exe or msi into MEM/SCCM is that a script installer allows for fine-grained control over the installation process. This is especially useful when dealing with porly written installers.

At the end of this article, I will provide a full break down of the script section by section.

---

## Full Script Example

```powershell

```

---

## Snippets

### Command Line Arguments

```powershell
param (
    [Alias("silent")]
    [switch]$s,

    [Alias("uninstall", "uninst", "u")]
    [switch]$x
)
```

Command line arguments can be passed to a Powershell script by using the `param` block at the beginning of the script. This block allows you to define the parameters that the script will accept. In this example, the script accepts two parameters: `$silent` and `$uninstall`. These parameters can be passed to the script when it is run from the command line. For example, `.\script.ps1 -silent "true" -uninstall "true"`. We use the [Alias()] attribute to define aliases for the parameters. This allows us to use different names for the parameters when running the script. For example, `-s` can be aliased to `-silent`, `-u` can be aliased to `-uninstall`. We also do not define a parameter for install, as the script will default to installing the program if no parameters are passed.

### Define Imutable Variables

```powershell
Set-Variable -Name "AppVendor" -Value "Google" -Option Constant
Set-Variable -Name "AppName" -Value "Chrome" -Option Constant
Set-Variable -Name "AppVersion" -Value "126.0.6478.127" -Option Constant
Set-Variable -Name "AppDisplayName" -Value "Google Chrome" -Option Constant
Set-Variable -Name "AppDisplayVersion" -Value "126.0.6478.127" -Option Constant
Set-Variable -Name "AppGUID" -Value "{A6A8A820-1396-378F-ABE5-4EBDEA19FE69}" -Option Constant
Set-Variable -Name "AppLogName" -Value "${AppVendor}_${AppName}_${AppVersion}.log" -Option Constant
Set-Variable -Name "LogPath" -Value "${Env:windir}\LogansLogs" -Option Constant
```

By using the `-Option Constant` flag, we are making these variables immutable, meaning that they cannot be changed once they are set. This is useful for defining constants that will be used throughout the script, following the DRY principle (Do Not Repeat Yourself). The benefit of using constants is that they provide a single point of control for values that are used multiple times in the script, allowing us to use the variables with certainty that the value will not change.

It is important to note that we are not repearing values. Insted they are two types of variables here that can be diferent from each other:

- Logging purposes: AppName, AppVersion
- Program purposes: AppDisplayName, AppDisplayVersion

### Logging

```powershell
if (-not (Test-Path -Path $LogPath)) {
    New-Item -Path $LogPath -ItemType Directory -Force
}
if (Test-Path -Path "${LogPath}\${AppLogName}") {
    Remove-Item -Path "${LogPath}\${AppLogName}" -Force
}
if (-not (Test-Path -Path "${LogPath}\${AppLogName}")) {
    New-Item -Path "${LogPath}\${AppLogName}" -ItemType File -Force
}
```

Here we add a function that will check for the existence of a log file and create one if it does not exist, additioanlly we will need to have the function create a log folder if it does not exist.

### Check to see if the program is already installed

```powershell
$installed = $false

# Check 64-bit registry
$regPath64 = "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\$appGUID"
if (Test-Path -Path $regPath64) {
    $app = Get-ItemProperty -Path $regPath64
    write-host "App: $($app.DisplayName) $($app.DisplayVersion)"
    if ($app.DisplayName -eq $AppDisplayName -and $app.DisplayVersion -eq $AppDisplayVersion) {
        $installed = $true
    }
}

# Check 32-bit registry if not already found
if (-not $installed) {
    $regPath32 = "HKLM:\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\$appGUID"
    if (Test-Path -Path $regPath32) {
        $app = Get-ItemProperty -Path $regPath32
        if ($app.DisplayName -eq $AppDisplayName -and $app.DisplayVersion -eq $AppDisplayVersion) {
            $installed = $true
        }
    }
}
```

### Error Handling When Calling External Programs

```powershell
try {
    # Running the msiexec program with example parameters
    $processInfo = Start-Process msiexec.exe -ArgumentList "/i C:\Path\To\Your\Installer.msi /quiet /norestart" -Wait -PassThru

    # Checking the exit code of the process
    if ($processInfo.ExitCode -eq 0) {
        Write-Host "Installation succeeded without requiring a reboot."
    }
    elseif ($processInfo.ExitCode -eq 3010) {
        Write-Host "Installation succeeded but a reboot is required."
        # Restart-Computer (restart the computer now)
        # $restart = $true (restart the computer later and using the $restart variable)
    }
    else {
        throw "Installation failed with exit code $($processInfo.ExitCode)."
    }
}
catch {
    # If an exception is thrown in the try block, it's caught here.
    Write-Host "An error occurred: $_"
    # Additional error handling can be performed here if needed.
}
```

The key takeaway from this script is the use of the `try` and `catch` blocks. The `try` block will attempt to run the code within the block. If an error occurs, the `catch` block will catch the error and run the code within the block. This is useful when calling external programs that may not return a 0 exit code when they run successfully. For example, the `msiexec` program will return a 0 exit code when it runs successfully, but it may return a 3010 exit code if a reboot is required. By using the `try` and `catch` blocks, we can catch the 3010 exit code and handle it accordingly.

---

## Additional Snippets

### Automagically Uninstall Programs

```powershell
# Retrieve uninstall information from the registry
$programsToUninstall = Get-ChildItem -Path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall, HKLM:\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall |

    # Extract properties of each registry key
    Get-ItemProperty |

    # Filter to only include items where the DisplayName matches "Program Name"
    Where-Object { ($_.DisplayName -match "Program Name")} |

    # Select the UninstallString and DisplayName for further processing
    Select-Object UninstallString, DisplayName
```

This script will search the registry for installed programs that have a displayname that matches "Program Name" and return the uninstall string and display name for each program into the `$programsToUninstall` array. There is a few ways that we can expand this script to be more useful. For example,

- Adding a foreach loop to modify the uninstall string to include the `/quiet` or `-s` flag (depending on the program that we are unisntalling). This will allow us to silently uninstall the program.
- Add more `Select-Object` properties to the script to include the `displayVersion` of the program. This will allow us to create fine-grained uninstall scripts that can target specific versions of a program. Additionally, we can use this information to add to a custom log file.

---

{{< comments >}}
