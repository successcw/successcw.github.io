---
layout: post
title: Call DLL from C in visual studio
date: 2018-07-29 08:00:00 +0800
description: # Add post description (optional)
img: # Add image post (optional)
categories: DLL
tags: [DLL]# add tag
---
## Create a new empty project in visual studio

* create empty project callDLL
<img src="{{site.baseurl}}/assets/img/call-dll-1.png" width="80%" height="80%">

* add main.c <br>
<img src="{{site.baseurl}}/assets/img/call-dll-2.png" width="80%" height="80%">

## parse ini file
* Parsing logic refers from [this code](https://github.com/benhoyt/inih), add it to callDLL
* add bellowing to main.c

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <Windows.h>
#include "ini.h"

typedef struct
{
        const char* strServerAddr;
        const char* strPassword;
} configuration;

static int handler(void* user, const char* section, const char* name,
        const char* value)
{
        configuration* pconfig = (configuration*)user;
#define MATCH(s, n) strcmp(section, s) == 0 && strcmp(name, n) == 0

        if (MATCH("Setting", "ServerAddr")) {
                pconfig->strServerAddr = _strdup(value);
        } else if (MATCH("Setting", "password")) {
                pconfig->strPassword = _strdup(value);
        } else {
                return 0;
        }
        return 1;
}

void main()
{
	configuration config;
        /* 1. parse config file*/
        if (ini_parse("Samples.ini", handler, &config) < 0) {
                printf("SFC_DB_Config.ini file not exist!\n");
                return 1;
        }
        printf("config loaded from 'SFC_DB_Config.ini': \n");
        printf("ServerAddr=%s\n", config.strServerAddr);
        printf("password=%s\n", config.strPassword);
}
```

Samples.ini
```ini

[Setting]
ServerAddr = 10.0.0.1
password = admin

```

## call DLL functions

* example.dll includes two functions

```c
char *CheckStatus(char *temp)
char *UpdateStatus(char *temp)
```

* add bellowing to main.c

```c
typedef char* (_stdcall *FnCheckStatus(char *temp, char* strServerAddr,
		char* strPassword);
typedef char* (_stdcall *FnUpdateStatus(char *temp);

HINSTANCE dll_handler;
FnCheckStatus pFnCheckStatus;
FnUpdateStatus pFnUpdateStatus;
char strTemp[1024];

char* returnResult;
dll_handler = LoadLibrary("example.dll");
if (dll_handler != NULL) {
    pFnCheckStatus = (FnCheckStatus)GetProcAddress(dll_handler, "CheckStatus");
    if (!pFncheckStatus) {
	FreeLibrary(dll_handler);
	printf("can't find interface CheckStatus\n");
    } else {
	snprintf(strTemp, sizeof(strTemp), "this is a test string");
	__try {
	    returnResult = pFnCheckStatus(strTemp, config.strServerAddr,
				config.strPassword);
	}
	__except (EXCEPTION_EXECUTE_HANDLER) {
	    printf("pFnCheckStatus error\n");
	}
    }
    pFnUpdateStatus = (FnUpdateStatus)GetProcAddress(dll_handler, "UpdateStatus");
    /*same logic with CheckStatus*/
    ...
}

```

