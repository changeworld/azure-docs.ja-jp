---
title: Azure Front Door Service - PowerShell のリファレンス | Microsoft Docs
description: このリファレンスでは、Azure Front Door Service でサポートされるさまざまな PowerShell コマンドレットについて詳しく説明します
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2018
ms.author: sharadag
Module Name: AzureRM.FrontDoor
online version: https://docs.microsoft.com/powershell/module/azurerm.frontdoor/new-azurermfrontdoorbackendpoolobject
schema: 2.0.0
ms.openlocfilehash: ccd34aa4398c15c87ae78483b28426588212bc95
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "47046509"
---
# New-AzureRmFrontDoorBackendPoolObject

Front Door を作成するために PSBackendPool オブジェクトを作成します

## 構文

```
New-AzureRmFrontDoorBackendPoolObject -ResourceGroupName <String> -Name <String> -FrontDoorName <String>
 -Backend <PSBackend[]> -LoadBalancingSettingsName <String> -HealthProbeSettingsName <String>
 [-DefaultProfile <IAzureContextContainer>] [<CommonParameters>]
```

## Description
Front Door を作成するために PSBackendPool オブジェクトを作成します

## 例

### 例 1
```powershell
PS C:\> New-AzureRmFrontDoorBackendPoolObject -Name "backendpool1" -FrontDoorName $Name -ResourceGroupName $resourceGroupName -Backend $backend1 -He
althProbeSettingsName "healthProbeSetting1" -LoadBalancingSettingsName "loadBalancingSetting1"


Backends                : {Microsoft.Azure.Commands.FrontDoor.Models.PSBackend}
LoadBalancingSettingRef : /subscriptions/{subid}/resourceGroups/{resourceGroupName}/providers
                          /Microsoft.Network/frontDoors/frontdoor5/LoadBalancingSettings/loadBalancingSetting1
HealthProbeSettingRef   : /subscriptions/{subid}/resourceGroups/{resourceGroupName}/providers
                          /Microsoft.Network/frontDoors/frontdoor5/HealthProbeSettings/healthProbeSetting1
EnabledState            : Enabled
ResourceState           :
Id                      :
Name                    : backendpool1
Type                    :
```

Front Door を作成するために PSBackendPool オブジェクトを作成します

## パラメーター

### -Backend
このプールのバックエンドのセット。

```yaml
Type: Microsoft.Azure.Commands.FrontDoor.Models.PSBackend[]
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -DefaultProfile
Azure との通信に使用される資格情報、アカウント、テナント、およびサブスクリプション。

```yaml
Type: Microsoft.Azure.Commands.Common.Authentication.Abstractions.IAzureContextContainer
Parameter Sets: (All)
Aliases: AzureRmContext, AzureCredential

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -FrontDoorName
このルーティング規則が属する Front Door の名前。

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -HealthProbeSettingsName
このバックエンド プールに対する正常性プローブの設定の名前

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -LoadBalancingSettingsName
このバックエンド プールに対する負荷分散の設定の名前

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -Name
BackendPool 名。

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -ResourceGroupName
RoutingRule が作成されるリソース グループの名前。

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### 共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「about_CommonParameters」(http://go.microsoft.com/fwlink/?LinkID=113216) を参照してください。
