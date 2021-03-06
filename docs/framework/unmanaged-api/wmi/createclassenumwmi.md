---
title: 'CreateClassEnumWmi 函数 (非托管 API 参考) '
description: CreateClassEnumWmi 函数为满足指定条件的所有类返回一个枚举器。
ms.date: 11/06/2017
api_name:
- CreateClassEnumWmi
api_location:
- WMINet_Utils.dll
api_type:
- DLLExport
f1_keywords:
- CreateClassEnumWmi
helpviewer_keywords:
- CreateClassEnumWmi function [.NET WMI and performance counters]
topic_type:
- Reference
ms.openlocfilehash: 5b80954e288f6720c75d0af0b8af083fa4856754
ms.sourcegitcommit: d8020797a6657d0fbbdff362b80300815f682f94
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95719731"
---
# <a name="createclassenumwmi-function"></a>CreateClassEnumWmi 函数

返回满足指定选择条件的所有类的枚举器。

[!INCLUDE[internalonly-unmanaged](../../../../includes/internalonly-unmanaged.md)]

## <a name="syntax"></a>语法

```cpp
HRESULT CreateClassEnumWmi (
   [in] BSTR                    strSuperclass,
   [in] long                    lFlags,
   [in] IWbemContext*           pCtx,
   [out] IEnumWbemClassObject** ppEnum,
   [in] DWORD                   authLevel,
   [in] DWORD                   impLevel,
   [in] IWbemServices*          pCurrentNamespace,
   [in] BSTR                    strUser,
   [in] BSTR                    strPassword,
   [in] BSTR                    strAuthority
);
```

## <a name="parameters"></a>参数

`strSuperclass`\
中如果不为 `null` 或空，则指定父类的名称; 枚举器仅返回此类的子类。 如果该 `null` 参数为或为空 `lFlags` ，并且为 WBEM_FLAG_SHALLOW，则仅返回 (类没有父类) 的顶级类。 如果为 `null` 或为空 `lFlags` ，并且为 `WBEM_FLAG_DEEP` ，则返回命名空间中的所有类。

`lFlags`\
中影响此函数的行为的标志的组合。 以下值是在 *WbemCli* 头文件中定义的，也可以在代码中将它们定义为常量：

|返回的常量  |Value  |说明  |
|---------|---------|---------|
| `WBEM_FLAG_USE_AMENDED_QUALIFIERS` | 0x20000 | 如果设置，则函数将检索存储在当前连接的区域设置的本地化命名空间中的已修改限定符。 <br/> 如果未设置，则函数仅检索直接命名空间中存储的限定符。 |
| `WBEM_FLAG_DEEP` | 0 | 枚举包括层次结构中的所有子类，但不包括此类。 |
| `WBEM_FLAG_SHALLOW` | 1 | 枚举仅包括此类的纯实例，并排除提供此类中未找到的属性的所有子类的实例。 |
| `WBEM_FLAG_RETURN_IMMEDIATELY` | 0x10 | 标志导致半同步调用。 |
| `WBEM_FLAG_FORWARD_ONLY` | 0x20 | 函数返回一个只进枚举器。 通常，只进枚举器比传统枚举器更快，使用的内存更少，但它们不允许调用 [克隆](clone.md)。 |
| `WBEM_FLAG_BIDIRECTIONAL` | 0 | WMI 保留指向枚举中的对象的指针，直到它们被释放。 |

建议的标志 `WBEM_FLAG_RETURN_IMMEDIATELY` 为， `WBEM_FLAG_FORWARD_ONLY` 为获得最佳性能。

`pCtx`\
中通常，此值为 `null` 。 否则，它是指向提供请求的类的提供程序可以使用的 [IWbemContext](/windows/desktop/api/wbemcli/nn-wbemcli-iwbemcontext) 实例的指针。

`ppEnum`\
弄接收指向枚举器的指针。

`authLevel`\
中授权级别。

`impLevel`\
中模拟级别。

`pCurrentNamespace`\
中指向表示当前命名空间的 [IWbemServices](/windows/desktop/api/wbemcli/nn-wbemcli-iwbemservices) 对象的指针。

`strUser`\
中用户名。 有关详细信息，请参阅 [ConnectServerWmi](connectserverwmi.md) 函数。

`strPassword`\
中密码。 有关详细信息，请参阅 [ConnectServerWmi](connectserverwmi.md) 函数。

`strAuthority`\
中用户的域名。 有关详细信息，请参阅 [ConnectServerWmi](connectserverwmi.md) 函数。

## <a name="return-value"></a>返回值

此函数返回的以下值是在 *WbemCli* 头文件中定义的，也可以在代码中将它们定义为常量：

|返回的常量  |Value  |说明  |
|---------|---------|---------|
| `WBEM_E_ACCESS_DENIED` | 0x80041003 | 用户无权查看函数可以返回的一个或多个类。 |
| `WBEM_E_FAILED` | 0x80041001 | 发生了未知错误。 |
| `WBEM_E_INVALID_CLASS` | 0x80041010 | `strSuperClass` 不存在。 |
| `WBEM_E_INVALID_PARAMETER` | 0x80041008 | 参数无效。 |
| `WBEM_E_OUT_OF_MEMORY` | 0x80041006 | 没有足够的可用内存来完成该操作。 |
| `WBEM_E_SHUTTING_DOWN` | 0x80041033 | WMI 可能已停止并重新启动。 再次调用 [ConnectServerWmi](connectserverwmi.md) 。 |
| `WBEM_E_TRANSPORT_FAILURE` | 0x80041015 | 远程过程调用 (RPC) 当前进程和 WMI 之间的链接已失败。 |
|`WBEM_S_NO_ERROR` | 0 | 函数调用成功。  |

## <a name="remarks"></a>注解

此函数包装对 [IWbemServices：： CreateClassEnum](/windows/desktop/api/wbemcli/nf-wbemcli-iwbemservices-createclassenum) 方法的调用。

如果函数调用失败，可以通过调用 [GetErrorInfo](geterrorinfo.md) 函数获取其他错误信息。

## <a name="requirements"></a>要求

**平台：** 请参阅 [系统要求](../../get-started/system-requirements.md)。

**标头：** WMINet_Utils .idl

**.NET Framework 版本：**[!INCLUDE[net_current_v472plus](../../../../includes/net-current-v472plus.md)]

## <a name="see-also"></a>另请参阅

- [WMI 和性能计数器（非托管 API 参考）](index.md)
