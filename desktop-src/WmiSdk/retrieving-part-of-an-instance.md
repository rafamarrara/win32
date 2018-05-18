---
Description: 'A partial-instance retrieval is when WMI retrieves only a subset of the properties of an instance.'
audience: developer
author: 'REDMOND\\markl'
manager: 'REDMOND\\markl'
ms.assetid: '6cc26b26-adc9-4a8a-b51e-9db94eb4295f'
ms.prod: 'windows-server-dev'
ms.technology: 'windows-management-instrumentation'
ms.tgt_platform: multiple
title: Retrieving Part of a WMI Instance
---

# Retrieving Part of a WMI Instance

A partial-instance retrieval is when WMI retrieves only a subset of the properties of an instance. For example, WMI could retrieve only the **Name** and **Key** properties. The most common use of partial-instance retrieval is on large enumerations that have multiple properties.

## Retrieving Part of a WMI Instance Using PowerShell

You can retrieve an individual property of an instance by using [Get-WmiObject](https://technet.microsoft.com/library/dd315379.aspx); the property itself can be retrieved and displayed a number of ways. As with retrieving an instance, PowerShell will by default return all instances of a given class; you must specify a specific value if you wish to retrieve only a single instance.

The following code example displays the volume serial number for an instance of the [**Win32\_LogicalDisk**](https://msdn.microsoft.com/library/aa394173) class.


```PowerShell
(Get-WmiObject -class Win32_logicalDisk).DeviceID

#or

Get-WmiObject -class Win32_LogicalDisk | format-list DeviceID

#or

$myDisk = Get-WmiObject -class Win32_LogicalDisk
$myDisk.DeviceID
```



## Retrieving Part of a WMI Instance Using C# (System.Management)

You can retrieve an individual property of an instance by creating a new [ManagementObject](https://msdn.microsoft.com/library/system.management.managementobject.aspx) using the details of a specific instance. You can then implicitly retrieve one or more properties of the instance with the [GetPropertyValue](https://msdn.microsoft.com/library/system.management.managementbaseobject.getpropertyvalue.aspx) method.

> [!Note]  
> **System.Management** was the original .NET namespace used to access WMI; however, the APIs in this namespace generally are slower and do not scale as well relative to their more modern **Microsoft.Management.Infrastructure** counterparts.

�

The following code example displays the volume serial number for an instance of the [**Win32\_LogicalDisk**](https://msdn.microsoft.com/library/aa394173) class.


```CSharp
using System.Management;
...
ManagementObject myDisk = new ManagementObject("Win32_LogicalDisk.DeviceID='C:'");
string myProperty = myDisk.GetPropertyValue("VolumeSerialNumber").ToString();
Console.WriteLine(myProperty);
```



## Retrieving Part of a WMI Instance Using VBScript

You can retrieve an individual property of an instance by using [**GetObject**](iwbemservices-getobject.md).

The following code example displays the volume serial number for an instance of the [**Win32\_LogicalDisk**](https://msdn.microsoft.com/library/aa394173) class.


```VB
MsgBox (GetObject("WinMgmts:Win32_LogicalDisk='C:'").VolumeSerialNumber)
```



## Retrieving Part of a WMI Instance Using C++

The following procedure is used to request a partial-instance retrieval using C++.

**To request a partial-instance retrieval using C++**

1.  Create an [**IWbemContext**](iwbemcontext.md) object with a call to [**CoCreateInstance**](_com_cocreateinstance).

    A context object is an object that WMI uses to pass in more information to a WMI provider. In this case, you are using the [**IWbemContext**](iwbemcontext.md) object to instruct the provider to process a partial-instance retrieval.

2.  Add \_\_GET\_EXTENSIONS, \_\_GET\_EXT\_CLIENT\_REQUEST, and any other named values that describe the properties you want to retrieve to the [**IWbemContext**](iwbemcontext.md) object.

    The following table lists the different named values use in your retrieval call.

    

    | Named value                              | Description                                                                                                                                                                                                                                                                 |
    |------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | \_\_GET\_EXTENSIONS<br/>           | **VT\_BOOL** set to **VARIANT\_TRUE**. Used to signal that partial-instance retrieval operations are being used. This allows a quick, single check without having to enumerate the entire context object. If any of the other values are used, this one must be.<br/> |
    | \_\_GET\_EXT\_PROPERTIES<br/>      | [**SAFEARRAY**](https://msdn.microsoft.com/library/windows/desktop/ms221482) of strings listing the properties to be retrieved. Cannot be simultaneously specified with \_\_GET\_EXT\_KEYS\_ONLY.<br/> An asterisk "\*" is an invalid property name for \_\_GET\_EXT\_PROPERTIES.<br/>                    |
    | \_\_GET\_EXT\_KEYS\_ONLY<br/>      | **VT\_BOOL** set to **VARIANT\_TRUE**. Indicates that only keys should be provided in the returned object. Cannot be simultaneously specified with \_\_GET\_EXT\_PROPERTIES. Instead, takes precedence over \_\_GET\_EXT\_PROPERTIES.<br/>                            |
    | \_\_GET\_EXT\_CLIENT\_REQUEST<br/> | **VT\_BOOL** set to **VARIANT\_TRUE**. Indicates that the caller was the one who wrote the value into the context object and that it was not propagated from another dependent operation.<br/>                                                                        |

    

    �

3.  Pass the [**IWbemContext**](iwbemcontext.md) context object into any calls to [**IWbemServices::GetObject**](iwbemservices-getobject.md), [**IWbemServices::GetObjectAsync**](iwbemservices-getobjectasync.md), [**IWbemServices::CreateInstanceEnum**](iwbemservices-createinstanceenum.md), or [**IWbemServices::CreateInstanceEnumAsync**](iwbemservices-createinstanceenumasync.md) through the *pCtx* parameter.

    Passing the [**IWbemContext**](iwbemcontext.md) object instructs the provider to allow partial-instance retrievals. In a full-instance retrieval, you would set *pCtx* to a **null** value. If the provider does not support partial-instance retrieval, you will receive an error message.

If the provider cannot comply with the partial-instance operation, the provider either proceeds as if you did not enter the context object, or else returns **WBEM\_E\_UNSUPPORTED\_PARAMETER**.

The following example describes how to perform a full instance retrieval of [**Win32\_LogicalDisk**](https://msdn.microsoft.com/library/aa394173), and then performs a partial-instance retrieval of the **Win32\_LogicalDisk.Caption** property.


```C++
#include <stdio.h>
#define _WIN32_DCOM
#include <wbemidl.h>
#pragma comment(lib, "wbemuuid.lib")
#include <iostream>
using namespace std;
#include <comdef.h>


void main(void)
{
    HRESULT hr;
    _bstr_t bstrNamespace;
    IWbemLocator *pWbemLocator = NULL;
    IWbemServices *pServices = NULL;
    IWbemClassObject *pDrive = NULL;
    

    hr = CoInitializeEx(NULL, COINIT_APARTMENTTHREADED);

    hr = CoInitializeSecurity(NULL, -1, NULL, NULL,
                         RPC_C_AUTHN_LEVEL_CONNECT,
                         RPC_C_IMP_LEVEL_IMPERSONATE,
                         NULL, EOAC_NONE, 0);
 
    if (FAILED(hr))
    {
       CoUninitialize();
       cout << "Failed to initialize security. Error code = 0x" 
            << hex << hr << endl;
       return;
    }

    hr = CoCreateInstance(CLSID_WbemLocator, NULL, 
                          CLSCTX_INPROC_SERVER, IID_IWbemLocator, 
                          (void**) &amp;pWbemLocator);
    if( FAILED(hr) ) 
    {
        CoUninitialize();
        printf("failed CoCreateInstance\n");
        return;
    }
    
    bstrNamespace = L"root\\cimv2";
    hr = pWbemLocator->ConnectServer(bstrNamespace, 
              NULL, NULL, NULL, 0, NULL, NULL, &amp;pServices);
    if( FAILED(hr) ) 
    {
        pWbemLocator->Release();
        CoUninitialize();
        printf("failed ConnectServer\n");
        return;
    }
    pWbemLocator->Release();
    printf("Successfully connected to namespace.\n");

    
    BSTR bstrPath = 
         SysAllocString(L"Win32_LogicalDisk.DeviceID=\"C:\"");
   // *******************************************************//
   // Perform a full-instance retrieval. 
   // *******************************************************//
    hr = pServices->GetObject(bstrPath,
                              0,0, &amp;pDrive, 0);
    if( FAILED(hr) )
    { 
        pServices->Release();
        CoUninitialize();
        printf("failed GetObject\n");
        return;
    }    
    // Display the object
    BSTR  bstrDriveObj;
    hr = pDrive->GetObjectText(0, &amp;bstrDriveObj);
    printf("%S\n\n", bstrDriveObj);

    pDrive->Release();
    pDrive = NULL;

   // *****************************************************//
   // Perform a partial-instance retrieval. 
   // *****************************************************//
    
    IWbemContext  *pctxDrive; // Create context object
    hr = CoCreateInstance(CLSID_WbemContext, NULL, 
                          CLSCTX_INPROC_SERVER, IID_IWbemContext, 
                          (void**) &amp;pctxDrive);
    if (FAILED(hr))
    {
        pServices->Release();
        pDrive->Release();    
        CoUninitialize();
        printf("create instance failed for context object.\n");
        return;
    }
    
    VARIANT  vExtensions;
    VARIANT  vClient;
    VARIANT  vPropertyList;
    VARIANT  vProperty;
    SAFEARRAY  *psaProperties;
    SAFEARRAYBOUND saBounds;
    LONG  lArrayIndex = 0;
    
    // Add named values to the context object. 
    VariantInit(&amp;vExtensions);
    V_VT(&amp;vExtensions) = VT_BOOL;
    V_BOOL(&amp;vExtensions) = VARIANT_TRUE;
    hr = pctxDrive->SetValue(_bstr_t(L"__GET_EXTENSIONS"), 
        0, &amp;vExtensions);
    VariantClear(&amp;vExtensions);

    VariantInit(&amp;vClient);
    V_VT(&amp;vClient) = VT_BOOL;
    V_BOOL(&amp;vClient) = VARIANT_TRUE;
    hr = pctxDrive->SetValue(_bstr_t(L"__GET_EXT_CLIENT_REQUEST"), 
       0, &amp;vClient);
    VariantClear(&amp;vClient);
    // Create an array of properties to return.
    saBounds.cElements = 1;
    saBounds.lLbound = 0;
    psaProperties = SafeArrayCreate(VT_BSTR, 1, &amp;saBounds);

    // Add the Caption property to the array.
    VariantInit(&amp;vProperty);
    V_VT(&amp;vProperty) = VT_BSTR;
    V_BSTR(&amp;vProperty) = _bstr_t(L"Caption");
    SafeArrayPutElement(psaProperties, &amp;lArrayIndex, 
       V_BSTR(&amp;vProperty));
    VariantClear(&amp;vProperty);
    
    VariantInit(&amp;vPropertyList);
    V_VT(&amp;vPropertyList) = VT_ARRAY | VT_BSTR;
    V_ARRAY(&amp;vPropertyList) = psaProperties;
    // Put the array in the named value __GET_EXT_PROPERTIES.
    hr = pctxDrive->SetValue(_bstr_t(L"__GET_EXT_PROPERTIES"), 
        0, &amp;vPropertyList);
    VariantClear(&amp;vPropertyList);
    SafeArrayDestroy(psaProperties);

    // Pass the context object as the pCtx parameter of GetObject.
    hr = pServices->GetObject(bstrPath, 0, pctxDrive, &amp;pDrive, 0);
    if( FAILED(hr) )
    { 
        pServices->Release();
        CoUninitialize();
        printf("failed property GetObject\n");
        return;
    }    
    // Display the object
    hr = pDrive->GetObjectText(0, &amp;bstrDriveObj);
    printf("%S\n\n", bstrDriveObj);

    SysFreeString(bstrPath);

    VARIANT vFileSystem;
    // Attempt to get a property that was not requested.
    // The following should return a NULL property if
    // the partial-instance retrieval succeeded.

    hr = pDrive->Get(_bstr_t(L"FileSystem"), 0,
                       &amp;vFileSystem, NULL, NULL);

    if( FAILED(hr) )
    { 
        pServices->Release();
        pDrive->Release();    
        CoUninitialize();
        printf("failed get for file system\n");
        return;
    }    
 
    if (V_VT(&amp;vFileSystem) == VT_NULL)
        printf("file system variable is null - expected\n");
    else
        printf("FileSystem = %S\n", V_BSTR(&amp;vFileSystem));
    
    VariantClear(&amp;vFileSystem);

    pDrive->Release();    
    pctxDrive->Release();
    pServices->Release();
    pServices = NULL;    // MUST be set to NULL
    CoUninitialize();
    return;  // -- program successfully completed
};
```



When executed, the previous code example writes the following information. The first object description is from the full-instance retrieval. The second object description is from the partial-instance retrieval. The last section shows that you receive a **null** value if you request a property that was not requested in the original [**GetObject**](iwbemservices-getobject.md) call.

``` syntax
Successfully connected to namespace

instance of Win32_LogicalDisk
{
        Caption = "C:";
        Compressed = FALSE;
        CreationClassName = "Win32_LogicalDisk";
        Description = "Local Fixed Disk";
        DeviceID = "C:";
        DriveType = 3;
        FileSystem = "NTFS";
        FreeSpace = "3085668352";
        MaximumComponentLength = 255;
        MediaType = 12;
        Name = "C:";
        Size = "4581445632";
        SupportsFileBasedCompression = TRUE;
        SystemCreationClassName = "Win32_ComputerSystem";
        SystemName = "TITUS";
        VolumeName = "titus-c";
        VolumeSerialNumber = "7CB4B90E";
};

instance of Win32_LogicalDisk
{
        Caption = "C:";
        CreationClassName = "Win32_LogicalDisk";
        Description = "Local Fixed Disk";
        DeviceID = "C:";
        DriveType = 3;
        MediaType = 12;
        Name = "C:";
        SystemCreationClassName = "Win32_ComputerSystem";
        SystemName = "MySystem";
};
```

The following output is generated by the previous example.

``` syntax
file system variable is null - expected
Press any key to continue
```

�

�



