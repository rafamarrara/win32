﻿---
Description: 'Start capturing state changes in a parameter block.'
ms.assetid: 'cdf6f572-1a21-4c1d-a113-13b48bacd060'
title: 'ID3DXEffect::BeginParameterBlock method'
---

# ID3DXEffect::BeginParameterBlock method

Start capturing state changes in a parameter block.

## Syntax


```C++
HRESULT BeginParameterBlock();
```



## Parameters

This method has no parameters.

## Return value

Type: **[**HRESULT**](455d07e9-52c3-4efb-a9dc-2955cbfd38cc)**

If the method succeeds, the return value is D3D\_OK. If the method fails, the return value can be one of the following: D3DERR\_INVALIDCALL, D3DXERR\_INVALIDDATA.

## Remarks

Capture effect parameter state changes until EndParameterBlock is called. Effect parameters include any state changes outside of a pass. Delete parameter blocks if they are no longer needed by calling DeleteParameterBlock.

## Requirements



|                    |                                                                                          |
|--------------------|------------------------------------------------------------------------------------------|
| Header<br/>  | <dl> <dt>D3DX9Effect.h</dt> </dl> |
| Library<br/> | <dl> <dt>D3dx9.lib</dt> </dl>     |



## See also

<dl> <dt>

[ID3DXEffect](id3dxeffect.md)
</dt> <dt>

[**ID3DXEffect::EndParameterBlock**](id3dxeffect--endparameterblock.md)
</dt> <dt>

[**ID3DXEffect::ApplyParameterBlock**](id3dxeffect--applyparameterblock.md)
</dt> <dt>

[**ID3DXEffect::DeleteParameterBlock**](id3dxeffect--deleteparameterblock.md)
</dt> </dl>

 

 



