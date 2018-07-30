---
layout: post
title: Wrapper c static lib to c++ dll for C# 
date: 2018-07-30 00:00:00 +0300
description: # Add post description (optional) 
img: # Add image post (optional)
tags: [Visual Studio, DLL, lib]# add tag
---
## Create DLL project in visual studio

* create VisualC++ DLL project sn_dll_test
<img src="{{site.baseurl}}/assets/img/wrapper-static-lib-1.png" width="80%" height="80%">
 
* add dependence, input "your_lib_name.lib" <br> 
<img src="{{site.baseurl}}/assets/img/wrapper-static-lib-2.png" width="80%" height="80%">

* change configuration type to DLL
<img src="{{site.baseurl}}/assets/img/wrapper-static-lib-3.png" width="80%" height="80%">

## add bellowing to sn_dll_test.cpp

```cpp

#include <stdio.h>
#include "your_lib_name.h"

extern "C"
{
        __declspec(dllexport) void DisplayHelloFromDLL()
        {
                printf("Hello from DLL!\n");
        }
        __declspec(dllexport) int your_lib_func1_d()
        {
                return your_lib_func1();
        }
        __declspec(dllexport) int your_lib_func2_d()
        {
                return your_lib_func2();
        }
}

```
compile and generate sn_dll_test.lib

## Create C# console app project
<img src="{{site.baseurl}}/assets/img/wrapper-static-lib-4.png" width="80%" height="80%">

## Change build type to "Allow unsafe code"
<img src="{{site.baseurl}}/assets/img/wrapper-static-lib-5.png" width="80%" height="80%">

## add bellowing code to Program.cs

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Runtime.InteropServices;     // DLL support

namespace ConsoleApp1
{
    class Program
    {
        [DllImport("Project1.dll")]
        static extern void DisplayHelloFromDLL();
	[DllImport("sn_dll_test.dll", CallingConvention = CallingConvention.Cdecl)]
        static extern int your_lib_func1_d();
        static void Main(string[] args)
        {
            int returnValue;
            Console.WriteLine("This is C# program");
            DisplayHelloFromDLL();
	    your_lib_func1_d();
        }
    }
}

```


