---
title: Azure RTOS 用のセキュリティ モジュール API
description: Azure RTOS 用のセキュリティ モジュールのリファレンス API。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: cec28f9290808836ec2dfd334b23fe8c76df03fc
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102120064"
---
# <a name="security-module-for-azure-rtos-api"></a>Azure RTOS 用のセキュリティ モジュール API 

この API は、Azure RTOS のセキュリティ モジュールでのみ使用することを目的としています。 その他のリソースについては、[Azure RTOS 用の セキュリティ モジュールに関する GitHub リソース](https://github.com/azure-rtos/azure-iot-preview/releases)を参照してください。 

## <a name="enable-security-module-for-azure-rtos"></a>Azure RTOS 用のセキュリティ モジュールを有効にする

**nx_azure_iot_security_module_enable**

### <a name="prototype"></a>プロトタイプ

```c
UINT nx_azure_iot_security_module_enable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>説明

このルーチンでは、Azure IoT セキュリティ モジュール サブシステムを有効にします。 内部ステート マシンによってセキュリティ イベントのコレクションが管理され、それらが Azure IoT Hub に送信されます。 データ コレクションを管理するために必要なのは、1 つの NX_AZURE_IOT_SECURITY_MODULE インスタンスだけです。

### <a name="parameters"></a>パラメーター

| 名前 | 説明 |
|---------|---------|
| nx_azure_iot_ptr  [in]    | `NX_AZURE_IOT` を指すポインター。  |

### <a name="return-values"></a>戻り値

|戻り値  |説明 |
|---------|---------|
|NX_AZURE_IOT_SUCCESS|   Azure IoT セキュリティ モジュールが正常に有効になりました。     |
|NX_AZURE_IOT_FAILURE   |  内部エラーのため、Azure IoT セキュリティ モジュールを有効にできませんでした。    |
|NX_AZURE_IOT_INVALID_PARAMETER   |  セキュリティ モジュールには、有効な #NX_AZURE_IOT インスタンスが必要です。      |

### <a name="allowed-from"></a>許可元

Threads

## <a name="disable-azure-iot-security-module"></a>Azure IoT セキュリティ モジュールを無効にする

**nx_azure_iot_security_module_disable**


### <a name="prototype"></a>プロトタイプ

```c
UINT nx_azure_iot_security_module_disable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>説明

このルーチンでは、Azure IoT セキュリティ モジュール サブシステムを無効にします。

### <a name="parameters"></a>パラメーター

| 名前 | 説明 |
|---------|---------|
| nx_azure_iot_ptr  [in]    | `NX_AZURE_IOT` を指すポインターです。 NULL の場合、シングルトン インスタンスは無効になります。 |

### <a name="return-values"></a>戻り値

|戻り値  |説明 |
|---------|---------|
|NX_AZURE_IOT_SUCCESS     |   Azure IoT セキュリティ モジュールが正常に無効にされた場合は成功です。      |
|NX_AZURE_IOT_INVALID_PARAMETER   |  Azure IoT Hub インスタンスがシングルトン複合インスタンスと異なります。       |
|NX_AZURE_IOT_FAILURE    |  内部エラーのため、Azure IoT セキュリティ モジュールを無効にできませんでした。       |

### <a name="allowed-from"></a>許可元

Threads


## <a name="next-steps"></a>次の手順

Azure RTOS セキュリティ モジュールの使用を開始する方法の詳細については、次の記事を参照してください。

- Defender for IoT RTOS セキュリティ モジュールの[概要](iot-security-azure-rtos.md)に関する記事を参照します。