---
title: "How to make custom types in PowerShell"
date: 2025-01-06
---

Why would you want to make a custom type in PowerShell? Well, there are a few reasons. One reason is that you may want to create a custom object that has specific properties and methods that are not available in the built-in types. In this example, we make a custom type that will access the Windows API to minimize all windows when a specific process is running.

## Code

```powershell
function MinimixeWindows {
    $Win32ShowWindowAsync = Add-Type -memberDefinition @"
    [D11Import ("user32.d11")]
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

$mimimizeJob = Start-Job -ScriptBlock { MinimixeWindows }
$setupProcess = Start-Process "C:\setup.exe" -ArgumentList "/s" -PassThru -Wait 
Stop-Job -Job $minimizeJob
Remove-Job -Job $minimizeJob
```

---

## Explanation

This script creates a custom type called `Win32ShowWindowAsync` that will access the `ShowWindowAsync` function in the `user32.dll` library. The `ShowWindowAsync` function will minimize a window when it is called with the `SW_MINIMIZE` parameter (6). The script then enters a loop that will continuously check for processes that match a regular expression. If a process is found, the script will get the main window handle of the process and call the `ShowWindowAsync` function to minimize the window.

The script then starts a background job that will run the `MinimizeWindows` function. The script then starts a setup process that will install a program silently. Once the setup process is complete, the script will stop the background job and remove it.