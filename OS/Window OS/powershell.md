# Sample profile

```powershell
Set-PSReadLineOption -predictionsource history

Function YarnAlias {
  npx yarn $args
}

New-Alias yarn YarnAlias

Function CdkAlias {
  npx cdk $args
}

New-Alias cdk CdkAlias

Function SudoAlias {
  gsudo $args
}

New-Alias sudo SudoAlias

# Chocolatey profile
$ChocolateyProfile = "$env:ChocolateyInstall\\helpers\\chocolateyProfile.psm1"
if (Test-Path($ChocolateyProfile)) {
  Import-Module "$ChocolateyProfile"
}

oh-my-posh init pwsh --config "$env:POSH_THEMES_PATH/onehalf.minimal.omp.json" | Invoke-Expression

Register-ArgumentCompleter -Native -CommandName aws -ScriptBlock {
    param($commandName, $wordToComplete, $cursorPosition)
        $env:COMP_LINE=$wordToComplete
        $env:COMP_POINT=$cursorPosition
        aws_completer.exe | ForEach-Object {
            [System.Management.Automation.CompletionResult]::new($_, $_, 'ParameterValue', $_)
        }
        Remove-Item Env:\\COMP_LINE
        Remove-Item Env:\\COMP_POINT
}
```