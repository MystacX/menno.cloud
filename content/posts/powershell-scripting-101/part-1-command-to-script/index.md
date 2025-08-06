---
title: "Part 1 - From Command to Script"
date: 2025-08-06
hero: part1.png
description: From Command to Script - The PowerShell Mindset Shift
menu:
  sidebar:
    name: Part 1 - Command to Script
    identifier: ps-scripting-101-1
    parent: powershell-scripting-101
    weight: 10
author:
  name: Menno Korthoff
math: true
---

Most sysadmins and cloud engineers start with PowerShell by running one-liners in the console: querying services, restarting computers, or pulling Azure resource data. It's fast, effective, and—until it’s not. The moment you want reusability, error handling, input validation, or logging, that one-liner becomes a liability. That’s where scripting comes in.

This post walks you through the mental shift from running *commands* to writing *scripts*—without assuming any prior scripting experience.

---

## Scripts vs. Commands

A command is what you type into the console. A script is a saved sequence of commands (typically in a `.ps1` file) that you can run, reuse, and version. It lives outside your head—and outside your clipboard history.

> :green_circle: Commands are for quick tasks.  
> :green_circle: Scripts are for consistency, repeatability, and automation.

---

## Your First Script: The Skeleton

Let’s say you regularly check the status of a Windows service. In the console, you might write:

```powershell
Get-Service -Name 'wuauserv'
```

To turn this into a script, create a `.ps1` file:

```powershell
# Check-ServiceStatus.ps1
param(
    [string]$ServiceName = 'wuauserv'
)

$service = Get-Service -Name $ServiceName
Write-Output "[$($service.Status)] $ServiceName on $env:COMPUTERNAME"
```

Save it and run with:

```powershell
.\Check-ServiceStatus.ps1 -ServiceName 'bits'
```

---

## Execution Policy 101

By default, PowerShell might block script execution. Set a permissive policy *per session* like this:

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process
```

You can also unblock individual `.ps1` files with:

```powershell
Unblock-File -Path .\Check-ServiceStatus.ps1
```

Avoid setting machine-wide policies unless you're fully aware of the implications.

---

## `Write-Output` vs. `Write-Host`

PowerShell has multiple ways to show output. Stick with this rule:

> 🔹 Use `Write-Output` to emit data
> 🔹 Use `Write-Host` only for interactive, cosmetic messages

Example:

```powershell
Write-Output "The script ran at $(Get-Date)"
Write-Host "Done!" -ForegroundColor Green
```

`Write-Output` allows redirection and piping, making it automation-friendly. `Write-Host` just paints the screen—nothing more.

---

## Why Bother with Scripts?

Let’s be blunt. You don’t write scripts because it’s fun. You do it because:

* You want to run the same task every Monday at 3:00 AM via Task Scheduler.
* You want to deploy 50 Azure resources the same way, every time.
* You want logs, not memory.

The shift is mental: scripting forces you to think in *steps*, not just *commands*.

---

## Next Steps

In the next post, we’ll dive into variables, data types, and flow control with `if`, `foreach`, and friends—building your toolbelt for real automation.

Until then:
Save your one-liners. Turn them into functions. Script it.
