# Exercise 4: IronPython Calling C# Method
In this exercise, we will use IronPython code to call a method in C#. This exercise aims to illustrate the interoperability between IronPython and C#.

1. Begin by defining a C# class called HelloWorld with a method called SayHello(string name). This method should print a custom greeting message incorporating the provided name.

```csharp
public class HelloWorld
{
    public void SayHello(string name)
    {
        Console.WriteLine($"Hello, {name} from C#!");
    }
}
```

2. Next, you need to make the HelloWorld class accessible to the IronPython engine. Add the HelloWorld class to the IronPython scope by setting a variable in the ScriptScope that refers to an instance of the HelloWorld class.

```csharp
ScriptEngine engine = Python.CreateEngine();
ScriptScope scope = engine.CreateScope();

// Make the HelloWorld class accessible to the IronPython code
scope.SetVariable("HelloWorld", new HelloWorld());
```

3. Now, write an IronPython function called greet_csharp that calls the SayHello method on the HelloWorld instance. You will also write another function called greet_python that prints a similar greeting from IronPython's side.

```python
def greet_python(name):
    print(f'Hello, {name} from Python!')

def greet_csharp(name):
    HelloWorld.SayHello(name)

name = 'Alice'
greet_python(name)
greet_csharp(name)
```

4. Execute the IronPython code and observe the output. You should see a greeting message from both Python and C#.

```csharp
string code = @"
def greet_python(name):
    print(f'Hello, {name} from Python!')

def greet_csharp(name):
    HelloWorld.SayHello(name)

name = 'Alice'
greet_python(name)
greet_csharp(name)
";
ScriptSource source = engine.CreateScriptSourceFromString(code);
source.Execute(scope);
```

5. Compile the entire program and execute using the method from Exercise 3.

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
            ScriptScope scope = engine.CreateScope();
            // Make the HelloWorld class accessible to the IronPython code
            scope.SetVariable("HelloWorld", new HelloWorld());

            string code = @"
def greet_python(name):
    print(f'Hello, {name} from Python!')

def greet_csharp(name):
    HelloWorld.SayHello(name)

name = 'Alice'
greet_python(name)
greet_csharp(name)
";
            ScriptSource source = engine.CreateScriptSourceFromString(code);
            source.Execute(scope);
        }
    }
    
    public class HelloWorld
    {
        public void SayHello(string name)
        {
            Console.WriteLine($"Hello, {name} from C#!");
        }
    }
}
```

