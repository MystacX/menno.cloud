---
title: "Part 2 - Variables, scope and flow control"
date: 2025-08-06
hero: part2.png
description: Variables, Scoping and Control Flow in Scripts
menu:
  sidebar:
    name: Part 2 - Variables, scoping, flow
    identifier: ps-scripting-101-2
    parent: powershell-scripting-101
    weight: 11
author:
  name: Menno Korthoff
math: true
---

Now that you’ve written your first `.ps1` script, it’s time to move past static commands. This post covers variables, scope, and flow control. The building blocks of decision-making in your scripts. Once you grasp these, you can write logic that adapts to different inputs, environments, or outcomes.

---

## Declaring and Using Variables

Variables in PowerShell always begin with a dollar sign:

```powershell
$ServiceName = 'wuauserv'
```

They’re typeless by default but support strong typing when needed:

```powershell
[string]$ComputerName = 'SRV01'
[int]$RetryCount = 3
```

Use `Write-Output` to display a variable’s value during script execution:

```powershell
Write-Output "Target computer: $ComputerName"
```

Variables are case-insensitive by default, but write them consistently for readability.

---

## Scope: The Underrated Gotcha

PowerShell has several variable scopes:

* `Global`: accessible anywhere (not recommended in scripts)
* `Script`: accessible throughout the current script file
* `Local`: default inside functions or loops
* `Private`: limited to the block in which it’s declared

In most cases, default `Local` scope is sufficient. But be mindful when working with nested functions or dot-sourcing.

Example:

```powershell
$global:VMName = 'TestVM'  # Avoid unless absolutely required
```

Use `Write-Output` instead of relying on global state to pass data between components.

---

## Conditional Logic: `if`, `elseif`, `else`

Control flow starts with branching. Here's a simple check for service status:

```powershell
$service = Get-Service -Name $ServiceName

if ($service.Status -eq 'Running') {
    Write-Output "$ServiceName is running."
}
elseif ($service.Status -eq 'Stopped') {
    Write-Output "$ServiceName is stopped."
}
else {
    Write-Output "$ServiceName is in an unexpected state: $($service.Status)"
}
```

Keep your conditionals clean and favor `-eq`, `-ne`, `-gt`, `-lt`, `-like`, and `-match` for comparisons. Don’t use `==`.

---

## Switch: A Cleaner Alternative

`switch` blocks simplify multiple matching conditions. They're basically a bunch of if-statements combined:

```powershell
switch ($service.Status) {
    'Running' { Write-Output "Service is up." }
    'Stopped' { Write-Output "Service is down." }
    default { Write-Output "Unknown state." }
}
```

Use `switch` when comparing one value against multiple known possibilities.

---

## Loops: `foreach`, `while`, `do-until`

### `foreach`

Use `foreach` when iterating through arrays or collections:

```powershell
$computers = @('SRV01', 'SRV02', 'SRV03')

foreach ($computer in $computers) {
    Write-Output "Pinging $computer..."
    Test-Connection -ComputerName $computer -Count 1
}
```

> ✅ Prefer `foreach` over `ForEach-Object` for clarity and performance in scripts.

### `while` and `do`

Use these for logic that depends on a condition:

```powershell
$count = 0
while ($count -lt 3) {
    Write-Output "Attempt $count"
    $count++
}
```

---

## Summary

You now know how to:

* Declare and use variables
* Control script flow with `if`, `switch`, and loops
* Avoid scope-related surprises
* Structure your script’s logic clearly and predictably

This is where your scripts start feeling like real programs—capable of reacting to input, evaluating outcomes, and making decisions.

---

## Coming Up

In Part 3, we’ll introduce **functions**: how to encapsulate your logic, pass parameters, and build reusable building blocks for clean, modular code.

Until then:
Break it down. Use control flow. Keep scripting.
