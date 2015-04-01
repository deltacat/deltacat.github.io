title: "在 .NET 程序中启用调试信息输出"
date: 2009-12-29 14:41:39
tags: 软件开发
id: 26
categories: 软件开发

---

我们在开发当中，有时需要添加一些调试信息，以便在脱离集成调试环境时捕捉错误。Win32 编程常用 OutputDebugString 这个函数配合 DebugView 等工具来在程序中插入调试信息。为了将调试信息写入日志，也有很多方法和第三方工具，比如著名的 log4cxx。

.Net 平台下，我们只需要简单的调用 Trace 和 Debug 这两个类，即可实现大部分调试输出。

<!--more-->

**一、****Trace ****和**** Debug ****的异同。**

Trace 与 Debug 类均位于 System.Diagnostics. 我们在使用时，只需要引入该 namespace，同时在任何需要的地方直接调用即可。最大的区别在于，Debug 类仅在 Debug 模式下编译有效，而 Trace 类在 Debug 和 Release 模式下均可工作。例如这段代码：

``` CSharp
if (null == param1) 
{ 
    Debug.Fail("Debug: Parameter is null!"); // A 
    return; 
}

Trace.TraceInformation("Trace: Application Started!"); // B

…
```

默认情况下，在 Debug 模式下编译，以上 A，B 两个均输出到调试环境中，比如，如果开启了DebugView，就可以看到类似如下的输出：

Debug: Parameter is null!&#160; 
Trace: Application Started! 

如果在 Release 模式下编译代码并运行，使用 DebugView 观察，两个输出均无。这是因为，默认情况下，编译器为 Debug 模式添加了 “DEBUG” 和 “TRACE” 两个条件，而 Relase 模式仅有 “TRACE” 条件。这是默认设置，可以手工修改，修改位置在 Project-&gt;Properties-&gt;Build。

既然 Trace 在 Release 模式也可以工作，为什么我们不能在 DebugView 中看到输出呢？这是因为，我们没有设置正确的 Listener。Debug 类默认输出至调试器。而 Trace 类仅在开启 DEBUG 参数模式下输出至调试器。我们可以自行制定 Trace 信息的输出，通过添加 Listener。

**二、****Listener**

Listerner，又称为监听器。打开 .NET 中 Debug 和 Trace 类的定义，可以看到他们都有一个集合属性 Listeners：

``` CSharp
public static TraceListenerCollection Listeners { get; }
```

这个属性用于注册监听器。实际上，无论 Debug 还是 Trace，都有一个默认的监听器。对于 Trace 来说，这个监听器是 Console，如果没有在程序中创建 Console 窗口，它的输出就是当你按下 F5 时，IDE 的 output 窗口。这在 Release 条件下仍然有效。

但是，我们要解决的，是脱离 IDE 环境时的调试信息输出，这就需要手工指定 Lisenter。

.Net 内置若干 Listener，常用的包括 ConsoleTraceListener，ConsoleTraceListener，XmlWriterTraceListener 等，使用方法很简单。比如我们想将 Trace 信息写入一个文本文件 MyApp.log，只要打开 app.config，加入以下代码行：

``` XML
<system.diagnostics>
  <trace autoflush="true" indentsize="4">
    <listeners>
      <add name="consoleListener"
        type="System.Diagnostics.ConsoleTraceListener"/>
      <add name="fileLogListener"
        type="System.Diagnostics.TextWriterTraceListener"
        initializeData="MyApp.log"/>
    </listeners>
  </trace>
</system.diagnostics>
```

重新编译并运行程序，在exe文件所在的目录下，会自动生成一个 MyApp.log 文件，里面就是想要的内容。

以上代码也可以在程序源代码中调用 Trace/Debug 类的成员函数来实现。但是为了保持源代码在不同配置下的一致性，便于我们随时取消 Trace 信息（比如产品发布），建议将这个工作放在 App.config 进行。

我们还可以通过实现 TraceListener 来定义自己的 Listener，这不在本文的讨论范围，有兴趣的同学可自行查阅 MSDN。

**三、参考资料：**

- [MSDN 中的 Trace 类](http://msdn.microsoft.com/en-us/library/system.diagnostics.tracelistener.aspx)；

- [Use of Listener in .NET Framework with C#](http://tutorial.visualstudioteamsystem.com/details.aspx?item=120)