# Exercise 5: Accessing PowerShell Runspace with IronPython
In this exercise, we'll use IronPython to create a PowerShell runspace and execute a simple PowerShell command. This exercise is intended to illustrate how IronPython can integrate with PowerShell, expanding its capabilities.

1. Create a Python file called `powershell.py` in the same folder as your IronPython console. (ex: C:\Program Files\IronPython 3.4)
2. Start by importing the necessary .NET libraries that allow IronPython to interact with PowerShell.

```python
clr.AddReference('System.Management.Automation')
from System.Management.Automation import PowerShell
```

3. After importing the necessary libraries, create a new instance of PowerShell.

```python
ps = PowerShell.Create()
```

4. Next, add the PowerShell command you wish to execute. In this example, we will use the Get-Process command, which retrieves the status of processes on a local or remote computer.

```python
ps.AddScript("Get-Process")
```

4. After adding the command, you can now invoke it and get the output. The results will be printed on the console.

```python
output = ps.Invoke()
print(output)
```

5. Finally, execute the entire IronPython script using your IronPython environment, ipy.exe. 

```python
clr.AddReference('System.Management.Automation')
from System.Management.Automation import PowerShell

ps = PowerShell.Create()
ps.AddScript("Get-Process")

output = ps.Invoke()
print(output)
```

6. Do this by opening a terminal from the folder. You should see a list of running processes on your machine. If you see the below result, then you have successfully accessed PowerShell runspace using IronPython, which could allow for executing complex PowerShell scripts and commands directly from your IronPython code.

```
.\ipy.exe powershell.py
<System.Collections.ObjectModel.Collection`1[[System.Management.Automation.PSObject, System.Management.Automation, Version=3.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35]] object at 0x000000000000002B [System.Collections.ObjectModel.Collection`1[System.Management.Automation.PSObject]]>
```
