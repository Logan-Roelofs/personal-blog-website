---
title: "Useful Powershell Snippits to Create Custom Installers/Uninstallers"
date: 2024-07-11
---

---

## The Basics

The following code snippets are useful for creating custom installers and uninstallers in Powershell. The advantage to creating script installers versus uploading a regular exe or msi is that a script installer allows for fine-grained control over the installation process. This is especially useful when dealing with porly written installers.

At the end of this article, I will provide an exaple of a full script that can be used to install/uninstall a program.

---

## Snippets

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

### Command Line Arguments

```powershell
param (
    [string]$silent,
    [string]$uninstall
)
```

Command line arguments can be passed to a Powershell script by using the `param` block at the beginning of the script. This block allows you to define the parameters that the script will accept. In this example, the script accepts two parameters: `$silent` and `$uninstall`. These parameters can be passed to the script when it is run from the command line. For example, `.\script.ps1 -silent "true" -uninstall "true"`.

## Full Example

```powershell

```

---

{{< comments >}}
