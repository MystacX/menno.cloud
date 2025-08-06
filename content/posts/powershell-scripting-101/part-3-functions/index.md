---
title: "Part 3 - Functions"
date: 2025-08-06
hero: part3.png
description: Your First Real Building Blocks
menu:
  sidebar:
    name: Part 3 - Functions
    identifier: ps-scripting-101-3
    parent: powershell-scripting-101
    weight: 12
author:
  name: Menno Korthoff
math: true
---

By now, you’ve written linear scripts with logic and variables. But eventually, you’ll want to reuse code, reduce repetition, and make your scripts easier to test and maintain. That’s where **functions** come in.

This post shows you how to write clean, reusable functions using PowerShell best practices, with parameter validation, output handling, and real-world context.

---

## Why Functions Matter

Functions let you encapsulate logic into a named block that:

* Can be reused multiple times
* Accepts input (parameters)
* Returns output (via `Write-Output`)
* Keeps your script clean and testable

Think of functions as **power tools** for scripting: they do one job, and do it well.

---

## Basic Function Syntax

Here’s a simple example:

```powershell
function Get-Greeting {
    param (
        [string]$Name
    )

    Write-Output "Hello, $Name!"
}
```

Call it like this:

```powershell
Get-Greeting -Name 'Menno'
```

---

## Use `[Parameter()]` for Clarity

Decorating your parameters helps PowerShell understand how to parse them—and helps *you* enforce clean usage.

```powershell
function Restart-ServiceSafe {
    param (
        [Parameter(Mandatory)]
        [string]$ServiceName
    )

    $service = Get-Service -Name $ServiceName -ErrorAction Stop
    if ($service.Status -eq 'Running') {
        Restart-Service -Name $ServiceName -Force
        Write-Output "$ServiceName restarted successfully."
    }
    else {
        Write-Output "$ServiceName is not running. No restart needed."
    }
}
```

Use `-ErrorAction Stop` to trap errors, especially inside functions.

---

## Input Validation = Fewer Headaches

Use attributes like `ValidateSet`, `ValidatePattern`, and `ValidateNotNullOrEmpty`:

```powershell
function Set-LogLevel {
    param (
        [Parameter(Mandatory)]
        [ValidateSet('Info', 'Warning', 'Error')]
        [string]$Level
    )

    Write-Output "Log level set to: $Level"
}
```

This ensures the function only accepts known-good input—no surprises, no garbage data.

---

## Return Values: `Write-Output` or Explicit `return`

PowerShell returns anything not captured or redirected. You can use `return`, but prefer `Write-Output` to make behavior consistent:

```powershell
function Get-ComputerUptime {
    $uptime = (Get-CimInstance Win32_OperatingSystem).LastBootUpTime
    Write-Output $uptime
}
```

> ⚠️ Avoid using `Write-Host` to return values—it just writes to the screen and vanishes.

---

## Real Example: Create an Azure Resource Group

This shows everything so far in practice:

```powershell
function New-AzResourceGroupSafe {
    param (
        [Parameter(Mandatory)]
        [string]$ResourceGroupName,

        [Parameter(Mandatory)]
        [string]$Location
    )

    if (-not (Get-AzResourceGroup -Name $ResourceGroupName -ErrorAction SilentlyContinue)) {
        New-AzResourceGroup -Name $ResourceGroupName -Location $Location | Out-Null
        Write-Output "Resource group '$ResourceGroupName' created in $Location."
    }
    else {
        Write-Output "Resource group '$ResourceGroupName' already exists."
    }
}
```

Usage:

```powershell
New-AzResourceGroupSafe -ResourceGroupName 'rg-demo' -Location 'westeurope'
```

---

## Summary

You now know how to:

* Write functions with parameters and validation
* Return data cleanly with `Write-Output`
* Reuse logic for maintainable scripts

Functions are where PowerShell scripting stops being "glue" and becomes "code." Embrace them.

---

## Next Up

In Part 4, we’ll tackle **error handling and logging**. The stuff that turns fragile scripts into production-ready tools.

Until then:
Write small. Write reusable. Write functions.
