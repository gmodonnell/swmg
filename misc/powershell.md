# PowerShell

The CLI with the weirdest naming conventions. Nothing is syntactically simple with PowerShell, but maybe that's just a sign I'm not familiar enough with it yet. PowerShell is written on the .NET framework, and is purpose-built as an automation tool for handling the administration of Windows networks. PowerShell runs groups of cmdlets invoked against a machine to fulfill this duty. You are going to pipe cmdlets together a lot when using PowerShell for administration. It is also possible to write .bat(ch) scripts to run PowerShell routines. > and >> work the same way in PowerShell as they do in Linux (write vs append).

#### Remoting

PowerShell Remoting is a native Windows RCE feature built on top of [WinRM](https://learn.microsoft.com/en-us/windows/win32/winrm/portal). Using the Invoke-Command cmdlet is one way to leverage PowerShell Remoting. This feature can present a huge threat to a network because it has to be enabled even within AD Domains. _PowerShell Remoting uses HTTP or HTTPS on TCP 5985 and 5986 respectively_.

#### Logging

There is a notorious logging issue with PowerShell. To combat this, defenders have to install [PowerShell Enhanced Logging](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about\_logging?view=powershell-5.1) or a similar application to allow themselves the ability to properly monitor PowerShell activity on their network. [Script Block Logging](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about\_script\_blocks?view=powershell-7.3) and Module Logging are also standard installs for Micro$oft Blue Teams. [Transcription Logging ](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.host/start-transcript?view=powershell-7.3)is also very useful but requires a lot of fine-tuning. The most important thing to remember is that _sysadmins must opt in to all these defensive measures and tune them effectively_. A combination of Script Block Logging and Transcription Logging will catch most incidents if tuned well. The use of these two modules is the budget solution for entities low on power or storage space.
