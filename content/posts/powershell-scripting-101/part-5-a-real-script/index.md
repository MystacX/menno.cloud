---
title: "Part 5 - A Real Script"
date: 2025-08-06
hero: part5.png
description: Bringing it all together - A real script from scratch
menu:
  sidebar:
    name: Part 5 - A Real Script
    identifier: ps-scripting-101-5
    parent: powershell-scripting-101
    weight: 14
author:
  name: Menno Korthoff
math: true
---

So far, you've learned how to think in scripts, declare variables, handle logic, structure functions, and manage errors and logging. In this final post, we’ll build a **complete script** that combines everything.

The script will:

* Accept input from a JSON file
* Validate and process that input
* Create Azure or local resources based on parameters
* Handle errors and log results
* Follow PowerShell best practices throughout

---

## Scenario: Provision Virtual Machines from JSON Input

Let’s say your team provides you with a JSON file like this:

```json
[
    {
        "Name": "VM-001",
        "Location": "westeurope",
        "Environment": "Azure"
    },
    {
        "Name": "VM-002",
        "Location": "LocalHost",
        "Environment": "Local"
    }
]
```

You want to:

* Parse it
* Create a resource group and VM in Azure, or log a local provisioning task
* Log each step and outcome

---

## Full Script

```powershell
# Provision-VMs.ps1

param (
    [Parameter(Mandatory)]
    [string]$InputFile,

    [string]$LogPath = "$PSScriptRoot\provision.log"
)

function Write-Log {
    param (
        [Parameter(Mandatory)][string]$Message
    )
    $timestamp = Get-Date -Format 'yyyy-MM-dd HH:mm:ss'
    "$timestamp`t$Message" | Add-Content -Path $LogPath
}

function New-VMFromConfig {
    param (
        [Parameter(Mandatory)][pscustomobject]$Config
    )

    try {
        $name = $Config.Name
        $location = $Config.Location
        $environment = $Config.Environment

        Write-Log -Message "Starting provisioning for $name in $environment"

        switch ($environment) {
            'Azure' {
                if (-not (Get-AzResourceGroup -Name "RG-$name" -ErrorAction SilentlyContinue)) {
                    New-AzResourceGroup -Name "RG-$name" -Location $location | Out-Null
                    Write-Log -Message "Created resource group RG-$name"
                }

                New-AzVM -Name $name -ResourceGroupName "RG-$name" -Location $location -Image 'Win2022AzureEdition' -Size 'Standard_B2s' -Credential (Get-Credential) -Verbose:$false | Out-Null
                Write-Log -Message "Created Azure VM $name in $location"
            }

            'Local' {
                Write-Log -Message "Simulating local VM deployment for $name"
                # Here you’d insert logic for Hyper-V or VMware provisioning
            }

            default {
                throw "Unknown environment '$environment' for VM $name"
            }
        }

        Write-Output "Provisioned: $name ($environment)"
    }
    catch {
        Write-Output "Error provisioning $($Config.Name): $_"
        Write-Log -Message "ERROR: $($Config.Name) - $_"
    }
}

# Validate input
if (-not (Test-Path -Path $InputFile)) {
    Write-Output "Input file not found: $InputFile"
    exit 1
}

# Process input
$jsonContent = Get-Content -Path $InputFile -Raw | ConvertFrom-Json

foreach ($vmConfig in $jsonContent) {
    New-VMFromConfig -Config $vmConfig
}

Write-Output "Provisioning complete."
Write-Log -Message "Provisioning run completed."
```

---

## How to Run It

1. Save the JSON config to `vmconfig.json`
2. Run the script like this:

```powershell
.\Provision-VMs.ps1 -InputFile ".\vmconfig.json"
```

If provisioning Azure VMs, you'll need:

* The Az PowerShell module
* Logged-in context via `Connect-AzAccount`
* Permissions to create resource groups and VMs

---

## Key Concepts Reinforced

* **Parameterization** via `param()` and `Mandatory`
* **Validation** with `Test-Path` and structured error throwing
* **Function encapsulation** for `Write-Log` and `New-VMFromConfig`
* **Logging** to file with timestamps
* **Error handling** with `try/catch`, no silent failures
* **Clear output** with `Write-Output`, never `Write-Host`

---

## Wrapping Up the Series

Congratulations! You’ve now built a fully functional, best-practice PowerShell script that's:

* Clean
* Reusable
* Maintainable
* Production-ready

From here, you can explore:

* Parameter sets
* Advanced pipeline support
* Class-based modules
* CI/CD integrations (e.g. GitHub Actions, Azure DevOps)

---

Until then:
Structure your scripts. Handle your errors. Leave a trail of clean logs. And never use `select` when you mean `Select-Object`.

PowerShell isn’t just a shell. It’s your automation engine.
