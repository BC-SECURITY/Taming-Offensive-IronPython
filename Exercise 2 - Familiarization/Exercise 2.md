# Exercise 2: Familiarization with IronPython
In this exercise, you'll create and run a simple IronPython script to help you become familiar with IronPython.

1. Open the IronPython Console (IPY) by searching for it in the search bar
2. Copy the following code into the terminal

```python
import System

def print_dotnet_version():
    print("Your .NET version is: ", System.Environment.Version.ToString())

print("Hello, IronPython World!")
print_dotnet_version()
```

3. This script imports the Common Language Runtime (CLR) module and the .NET's System namespace. It then defines and calls a function print_dotnet_version() that prints the version of the .NET runtime on your system. The script also prints "Hello, IronPython World!" to the console.
4. The console should output something like below

```
Hello, IronPython World!
Your .NET version is:  [Your .NET version here]
```

5. Experiment with different commands and functions. Try importing other .NET libraries and using them in your IronPython script. This will give you a good sense of the kind of interactivity and interoperability IronPython offers with the .NET Framework.
