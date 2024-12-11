## PS-Remoting

*  We can make use of the `Get-Command` cmdlet with the `ParameterName` parameter to identify commands that include the `ComputerName` parameter


```powershell
Get-Command -ParameterName ComputerName
```

![Screenshot From 2024-12-11 22-55-58](https://github.com/user-attachments/assets/3b1e8c2f-7513-4613-b445-362a0aae4680)


* use the `Enable-PSRemoting` cmdlet to enable PowerShell remoting

```Powershell
Enable-PSRemoting
```

* Save the credentials 
```Powershell
 $Cred = Get-Credential
```

![Screenshot From 2024-12-11 22-58-02](https://github.com/user-attachments/assets/99c9bc1f-bdec-48cc-afb3-b7460a6053e3)


### One to One Remoting

```Powershell
Enter-PSSession -ComputerName DC-01 -Credential $Cred
```

![Screenshot From 2024-12-11 22-57-27](https://github.com/user-attachments/assets/021fad87-7f6f-4cd2-ad4a-b2d93d521def)


*References*

https://learn.microsoft.com/en-us/powershell/scripting/learn/ps101/08-powershell-remoting?view=powershell-7.4&viewFallbackFrom=powershell-7.2
