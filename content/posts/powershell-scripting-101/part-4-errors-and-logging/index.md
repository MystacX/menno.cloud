---
title: "Part 4 - Error Handling and Logs"
date: 2025-08-06
hero: part4.png
description: Error Handling and Logging Like a Pro
menu:
  sidebar:
    name: Part 4 - Error Handling and Logs
    identifier: ps-scripting-101-4
    parent: powershell-scripting-101
    weight: 13
author:
  name: Menno Korthoff
math: true
---

Once your scripts go beyond test labs and start interacting with live environments—local systems, production servers, Azure resources. They *will* encounter errors. That’s not failure. That’s reality. What matters is how you handle it.

This post covers clean, predictable error handling and structured logging. We’ll move beyond `-ErrorAction SilentlyContinue` and into something maintainable.

---

## The Wrong Way: Swallowing Errors

This pattern is everywhere in beginner scripts:

```powershell
Get-Service -Name 'InvalidName' -ErrorAction SilentlyContinue
```

It suppresses the error and gives you no feedback. That’s not handling an error—that's *ignoring* one.

---

## Try/Catch/Finally: The Real Deal

PowerShell supports structured exception handling with `try`, `catch`, and `finally`.

```powershell
try {
    $service = Get-Service -Name 'InvalidName' -ErrorAction Stop
    Write-Output "Service status: $($service.Status)"
}
catch {
    Write-Output "Error: $_"
}
finally {
    Write-Output "Checked service at $(Get-Date)"
}
```

### Key points

* Always use `-ErrorAction Stop` inside `try` blocks.
* The `catch` block uses `$_` to reference the error.
* `finally` runs whether the `try` succeeds or fails—great for cleanup or logging.

---

## Detecting Silent Failures

Sometimes, you can’t rely on exceptions. Use `$?`, `$LASTEXITCODE`, or `Test-Path` when needed.

```powershell
Remove-Item -Path 'C:\Temp\file.txt' -Force
if (-not $?) {
    Write-Output "Failed to delete file."
}
```

But whenever possible, prefer structured `try/catch`.

---

## Writing Logs

Logging should:

1. Be timestamped
2. Go to a file
3. Be optional or configurable

### Minimal pattern

```powershell
function Write-Log {
    param (
        [Parameter(Mandatory)]
        [string]$Message,

        [string]$LogPath = "$PSScriptRoot\script.log"
    )

    $timestamp = Get-Date -Format 'yyyy-MM-dd HH:mm:ss'
    "$timestamp`t$Message" | Add-Content -Path $LogPath
}
```

### Usage

```powershell
Write-Log -Message "Starting service check"
```

> :green-circle: Always use `Write-Output` for runtime messages and `Write-Log` for persistent ones.

---

## A Real Example: Error + Logging Together

```powershell
function Restart-ServiceSafe {
    param (
        [Parameter(Mandatory)]
        [string]$ServiceName
    )

    try {
        $service = Get-Service -Name $ServiceName -ErrorAction Stop
        if ($service.Status -eq 'Running') {
            Restart-Service -Name $ServiceName -Force
            Write-Output "$ServiceName restarted."
            Write-Log -Message "$ServiceName restarted successfully."
        }
        else {
            Write-Output "$ServiceName is not running. Skipped restart."
            Write-Log -Message "$ServiceName was not running."
        }
    }
    catch {
        Write-Output "Error: $_"
        Write-Log -Message "Failed to restart $ServiceName - $_"
    }
}
```

---

## Summary

You now know how to:

* Use `try/catch/finally` for real error handling
* Avoid silent failures with `$?`, `Test-Path`, etc.
* Write consistent, timestamped log entries to a file

Error handling and logging aren't optional in production scripts. They’re what *make* it production-ready.

---

## Up Next

In Part 5, we’ll bring everything together—variables, functions, error handling, input, and logging—into one **end-to-end PowerShell script** solving a real-world task.

Until then:
Handle your errors. Log your decisions. Script like you mean it.
