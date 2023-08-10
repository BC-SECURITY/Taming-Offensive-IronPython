# Exercise 3: Invoking IronPython in C# with Visual Studio Community
In this exercise, you will learn how to use Visual Studio Community to create a C# program that invokes an IronPython script. This exercise will demonstrate the interoperability between IronPython and C#.

1. Open Visual Studio Community and create a new console application project in C#. Name the project as per your preference.
2. Right-click on your project in the Solution Explorer and select "Manage NuGet Packages". Search for "IronPython" in the browse tab and install the latest version.

*Note: Skip if you did this earlier or are using the provided range.*

3. Open the Program.cs file in your project and replace its content with the following code that creates an instance of ScriptEngine, which is provided by the IronPython library to execute Python code. It then defines an IronPython script as a string and executes it.


```csharp
using IronPython.Hosting;
using Microsoft.Scripting.Hosting;
using System;

namespace YourNamespace
{
    class Program
    {
        static void Main(string[] args)
        {
            CallIronPython();
            Console.WriteLine("Press enter to continue...");
            Console.ReadLine();
        }

        static void CallIronPython()
        {
            ScriptEngine engine = Python.CreateEngine();
            string code = @"
import clr
import System

def print_dotnet_version():
    print('Your .NET version is: ', System.Environment.Version.ToString())

print('Hello, IronPython World!')
print_dotnet_version()";
            ScriptSource source = engine.CreateScriptSourceFromString(code);
            source.Execute();
        }
    }
}
```
*Note: Don't forget to replace YourNamespace with the actual namespace of your project.*

4. Save your changes and build the program using Build and Build Solution
5. Right-click on the Solution Explorer and select Open Folder in File Explorer
6. Navigate to your application folder, then bin, debug, <filename>.x
7. Double-click the application executable
8. You should see the following output in your console:

```
Hello, IronPython World!
Your .NET version is:  [Your .NET version here]
Press enter to continue...
```

9. Modify the IronPython script to perform other tasks or interact with other .NET libraries.
