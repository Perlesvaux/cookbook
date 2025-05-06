---
layout: post
title:  "Unit Testing (C#)"
date:   2024-06-11 17:49:02 -0600
categories: unit testing
tags: unit testing cs csharp dotnet framework linux
---
* Table of contents
{:toc}

## Installation

 Install the .NET Software Development Kit & Entity Framework Core tools:

```bash
sudo apt install dotnet-host-7.0
```

```bash
sudo apt install dotnet-sdk-7.0
```

## Create new project
Create a new **solution**
```bash
dotnet new sln -o myProject
```
```bash
cd myProject
```

Inside your project, create a new **classlib** and **xunit**
```bash
dotnet new classlib -o Fun
```
```bash
dotnet new xunit -o Fun.Test
```

... Add them to your **solution**
```bash
dotnet sln add Fun && dotnet sln add Fun.Test
```
... Inside the test, make a reference to the **classlib**
```bash
dotnet add Fun.Test reference Fun
```
Run the test from the root of your project
```bash
dotnet test
```

## Unit testing sample
 Function example **Fun/Class1.cs**
```csharp
namespace Fun;
public class Class1
{
  public string SayAnything() => "Hello World!";
}
```

Test example **Fun.Test/UnitTest1.cs**
```csharp
namespace Fun.Test;
using Fun;

public class UnitTest1
{
    [Fact]
    public void Test1()
    {
      string class1 = new Class1().SayAnything();
      Assert.True(class1.Equals("Hello World!"), "Both strings should be equal");

    }
}
```
## Assumed project structure
```
myProject/
├── myProject.sln
├── Fun
│   ├── Fun.csproj
│   └── Class1.cs
└── Fun.Test
    ├── Fun.Test.csproj
    ├── UnitTest1.cs
    └── Usings.cs
```
