## PS-Remoting

*  We can make use of the `Get-Command` cmdlet with the `ParameterName` parameter to identify commands that include the `ComputerName` parameter


```powershell
Get-Command -ParameterName ComputerName
```

![[Pasted image 20241211225615.png]]

* use the `Enable-PSRemoting` cmdlet to enable PowerShell remoting

```Powershell
Enable-PSRemoting
```

* Save the credentials 
```Powershell
 $Cred = Get-Credential
```

![[Pasted image 20241211225805.png]]

### One to One Remoting

```Powershell
Enter-PSSession -ComputerName DC-01 -Credential $Cred
```

![[Pasted image 20241211225739.png]]

*References*
https://learn.microsoft.com/en-us/powershell/scripting/learn/ps101/08-powershell-remoting?view=powershell-7.4&viewFallbackFrom=powershell-7.2
