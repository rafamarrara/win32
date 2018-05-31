---
title: CDM\_HIDECONTROL message
description: Hides the specified control in an Explorer-style Open or Save As dialog box.
ms.assetid: 5bf7f861-d38c-491a-89f0-5b3dfce8abfc
keywords:
- CDM_HIDECONTROL message Dialog Boxes
topic_type:
- apiref
api_name:
- CDM_HIDECONTROL
api_location:
- Commdlg.h
api_type:
- HeaderDef
ms.date: 05/31/2018
ms.topic: article
ms.author: windowssdkdev
ms.prod: windows
ms.technology: desktop
---

# CDM\_HIDECONTROL message

\[Starting with Windows Vista, the **Open** and **Save As** common dialog boxes have been superseded by the [Common Item Dialog](_shell_common_file_dialog). We recommended that you use the Common Item Dialog API instead of these dialog boxes from the Common Dialog Box Library.\]

Hides the specified control in an Explorer-style **Open** or **Save As** dialog box. The dialog box must have been created with the **OFN\_EXPLORER** flag; otherwise, the message fails.


```C++
#define WM_USER                  0x0400
#define CDM_FIRST               (WM_USER + 100)
#define CDM_HIDECONTROL         (CDM_FIRST + 0x0005)
```



## Parameters

<dl> <dt>

*wParam* 
</dt> <dd>

The identifier of the control to be hidden.

</dd> <dt>

*lParam* 
</dt> <dd>

This parameter is not used.

</dd> </dl>

## Return value

This message has no return value.

## Remarks

The corresponding macro is as follows:

``` syntax
void CommDlg_OpenSave_HideControl(hwnd, wparam);
```

## Requirements



|                                     |                                                                                                          |
|-------------------------------------|----------------------------------------------------------------------------------------------------------|
| Minimum supported client<br/> | Windows 2000 Professional \[desktop apps only\]<br/>                                               |
| Minimum supported server<br/> | Windows 2000 Server \[desktop apps only\]<br/>                                                     |
| Header<br/>                   | <dl> <dt>Commdlg.h (include Windows.h)</dt> </dl> |



## See also

<dl> <dt>

**Reference**
</dt> <dt>

[**GetOpenFileName**](/windows/win32/Commdlg/nf-commdlg-getopenfilenamea?branch=master)
</dt> <dt>

[**GetSaveFileName**](/windows/win32/Commdlg/nf-commdlg-getsavefilenamea?branch=master)
</dt> <dt>

[**OPENFILENAME**](/windows/win32/Commdlg/ns-commdlg-tagofna?branch=master)
</dt> <dt>

**Conceptual**
</dt> <dt>

[Common Dialog Box Library](common-dialog-box-library.md)
</dt> </dl>

 

 




