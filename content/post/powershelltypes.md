---
title: "How to make custom types in PowerShell"
date: 2025-01-06
---

Why would you want to make a custom type in PowerShell? Well, there are a few reasons. One reason is that you may want to create a custom object that has specific properties and methods not available in the built-in types. In this example, we create a custom type that accesses the Windows API to minimize all windows when a specific process is running.

## Code

```powershell
function MinimixeWindows {
    $Win32ShowWindowAsync = Add-Type -memberDefinition @"
    [DllImport ("user32.dll")]
    public static extern bool ShowWindowAsync(IntPtr hWnd, int nCmdShow);
"@ -name "Win32ShowWindowAsync" -namespace Win32Functions -passThru
    while ($true)
    {
        $regex = "" # Regular expression to match processes name that will be minimized
        $processes = Get-Process | Where-Object { $_.Name -match $regex }
        foreach ($process in $processes)
        {
            $hwnd = $process.MainWindowHandle
            if ($hwnd -ne 0)
            {
                $Win32ShowWindowAsync::ShowWindowAsync($hwnd, 6)
            }
        }
    }
}

$minimizeJob = Start-Job -ScriptBlock { MinimizeWindows }
$setupProcess = Start-Process "C:\setup.exe" -ArgumentList "/s" -PassThru -Wait 
Stop-Job -Job $minimizeJob
Remove-Job -Job $minimizeJob
```

---

## Explanation

This script creates a custom type called Win32ShowWindowAsync that accesses the ShowWindowAsync function from the user32.dll library. The ShowWindowAsync function minimizes a window when called with the SW_MINIMIZE parameter (6).

The script enters a loop that continuously checks for processes matching a regular expression. If a matching process is found, it retrieves the main window handle of the process and calls ShowWindowAsync to minimize the window.

The script starts a background job that runs the MinimizeWindows function. It then launches a setup process that installs a program silently. Once the setup completes, the script stops and removes the background job.