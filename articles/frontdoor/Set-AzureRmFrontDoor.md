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
online version: https://docs.microsoft.com/powershell/module/azurerm.frontdoor/set-azurermfrontdoor
schema: 2.0.0
ms.openlocfilehash: a0f9316294601556273c5694c8276b9e4742002a
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "47047210"
---
# Set-AzureRmFrontDoor
Front Door のロード バランサーを更新します

## 構文

### ByFieldsParameterSet (既定値)
```
Set-AzureRmFrontDoor -ResourceGroupName <String> -Name <String> [-RoutingRule <PSRoutingRule[]>]
 [-BackendPool <PSBackendPool[]>] [-FrontendEndpoint <PSFrontendEndpoint[]>]
 [-LoadBalancingSetting <PSLoadBalancingSetting[]>] [-HealthProbeSetting <PSHealthProbeSetting[]>]
 [-Tag <Hashtable>] [-EnabledState <PSEnabledState>] [-DefaultProfile <IAzureContextContainer>] [-WhatIf]
 [-Confirm] [<CommonParameters>]
```

### ByObjectParameterSet
```
Set-AzureRmFrontDoor -InputObject <PSFrontDoor> [-RoutingRule <PSRoutingRule[]>]
 [-BackendPool <PSBackendPool[]>] [-FrontendEndpoint <PSFrontendEndpoint[]>]
 [-LoadBalancingSetting <PSLoadBalancingSetting[]>] [-HealthProbeSetting <PSHealthProbeSetting[]>]
 [-Tag <Hashtable>] [-EnabledState <PSEnabledState>] [-DefaultProfile <IAzureContextContainer>] [-WhatIf]
 [-Confirm] [<CommonParameters>]
```

### ResourceIdParameterSet
```
Set-AzureRmFrontDoor -ResourceId <String> [-RoutingRule <PSRoutingRule[]>] [-BackendPool <PSBackendPool[]>]
 [-FrontendEndpoint <PSFrontendEndpoint[]>] [-LoadBalancingSetting <PSLoadBalancingSetting[]>]
 [-HealthProbeSetting <PSHealthProbeSetting[]>] [-Tag <Hashtable>] [-EnabledState <PSEnabledState>]
 [-DefaultProfile <IAzureContextContainer>] [-WhatIf] [-Confirm] [<CommonParameters>]
```

## Description
**Set-AzureRmFrontDoor** コマンドレットでは、Front Door ロード バランサーが更新されます。 入力パラメーターが指定されていない場合は、既存の Front Door の古いパラメーターが使用されます。

## 例

### 例 1: FrontDoorName および ResourceGroupName で既存の Front Door を更新する。
```powershell
PS C:\> Set-AzureRmFrontDoor -Name "frontDoor1" -ResourceGroupName "resourceGroup1" -RoutingRule $routingrule1 -BackendPool $backendpool1 -FrontendEndpoint $frontendEndpoint1 -LoadBalancingSetting $loadBalancingSetting1 -HealthProbeSetting $healthProbeSetting1

FriendlyName          : frontdoor1
RoutingRules          : {routingrule1}
BackendPools          : {backendpool1}
HealthProbeSettings   : {healthProbeSetting1}
LoadBalancingSettings : {loadbalancingsetting1}
FrontendEndpoints     : {frontendendpoint1}
EnabledState          : Enabled
ResourceState         : Enabled
ProvisioningState     : Succeeded
Cname                 :
Tags                  : {tag1, tag2}
Id                    : /subscriptions/{guid}/resourcegroups/{guid}/providers/M
                        icrosoft.Network/frontdoors/frontdoor1
Name                  : frontdoor1
Type                  : Microsoft.Network/frontdoor1
```

既存の FrontDoor を更新します。

### 例 2: PSFrontDoor オブジェクトで既存の Front Door を更新する。
```powershell
PS C:\>  Set-AzureRmFrontDoor -InputObject $frontDoor1 -RoutingRule $routingrule1 -BackendPool $backendpool1 -FrontendEndpoint $frontendEndpoint1 -LoadBalancingSetting $loadBalancingSetting1 -HealthProbeSetting $healthProbeSetting1

FriendlyName          : frontdoor1
RoutingRules          : {routingrule1}
BackendPools          : {backendpool1}
HealthProbeSettings   : {healthProbeSetting1}
LoadBalancingSettings : {loadbalancingsetting1}
FrontendEndpoints     : {frontendendpoint1}
EnabledState          : Enabled
ResourceState         : Enabled
ProvisioningState     : Succeeded
Cname                 :
Tags                  : {tag1, tag2}
Id                    : /subscriptions/{guid}/resourcegroups/{guid}/providers/M
                        icrosoft.Network/frontdoors/frontdoor1
Name                  : frontdoor1
Type                  : Microsoft.Network/frontdoor1
```

既存の FrontDoor を更新します。

### 例 3: ResourceId で既存の Front Door を更新する
```powershell
PS C:\>  Set-AzureRmFrontDoor -ResourceId {resourceId} -RoutingRule $routingrule1 -BackendPool $backendpool1 -FrontendEndpoint $frontendEndpoint1 -LoadBalancingSetting $loadBalancingSetting1 -HealthProbeSetting $healthProbeSetting1

FriendlyName          : frontdoor1
RoutingRules          : {routingrule1}
BackendPools          : {backendpool1}
HealthProbeSettings   : {healthProbeSetting1}
LoadBalancingSettings : {loadbalancingsetting1}
FrontendEndpoints     : {frontendendpoint1}
EnabledState          : Enabled
ResourceState         : Enabled
ProvisioningState     : Succeeded
Cname                 :
Tags                  : {tag1, tag2}
Id                    : /subscriptions/{guid}/resourcegroups/{guid}/providers/M
                        icrosoft.Network/frontdoors/frontdoor1
Name                  : frontdoor1
Type                  : Microsoft.Network/frontdoor1
```

既存の FrontDoor を更新します。

## パラメーター

### -BackendPool
ルーティング規則で使用できるバックエンド プール。

```yaml
Type: Microsoft.Azure.Commands.FrontDoor.Models.PSBackendPool[]
Parameter Sets: (All)
Aliases:

Required: False
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

### -EnabledState
Front Door ロード バランサーの運用状態。

```yaml
Type: Microsoft.Azure.Commands.FrontDoor.Models.PSEnabledState
Parameter Sets: (All)
Aliases:
Accepted values: Enabled, Disabled

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -FrontendEndpoint
ルーティング規則で使用できるフロントエンドのエンドポイント。

```yaml
Type: Microsoft.Azure.Commands.FrontDoor.Models.PSFrontendEndpoint[]
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -HealthProbeSetting
この Front Door インスタンスに関連付けられている正常性プローブの設定。

```yaml
Type: Microsoft.Azure.Commands.FrontDoor.Models.PSHealthProbeSetting[]
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -InputObject
更新する Front Door オブジェクト。

```yaml
Type: Microsoft.Azure.Commands.FrontDoor.Models.PSFrontDoor
Parameter Sets: ByObjectParameterSet
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: True (ByValue)
Accept wildcard characters: False
```

### -LoadBalancingSetting
この Front Door インスタンスに関連付けられている負荷分散の設定。

```yaml
Type: Microsoft.Azure.Commands.FrontDoor.Models.PSLoadBalancingSetting[]
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -Name
更新する Front Door の名前。

```yaml
Type: System.String
Parameter Sets: ByFieldsParameterSet
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -ResourceGroupName
Front Door が属するリソース グループ。

```yaml
Type: System.String
Parameter Sets: ByFieldsParameterSet
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -ResourceId
更新する Front Door のリソース ID

```yaml
Type: System.String
Parameter Sets: ResourceIdParameterSet
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```

### -RoutingRule
この FrontDoor に関連付けられているルーティング規則

```yaml
Type: Microsoft.Azure.Commands.FrontDoor.Models.PSRoutingRule[]
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -Tag
FrontDoor に関連付けられているタグ。

```yaml
Type: System.Collections.Hashtable
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -Confirm
コマンドレットの実行前に確認を求めるメッセージが表示されます。

```yaml
Type: System.Management.Automation.SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -WhatIf
コマンドレットの実行時に発生する内容を示します。
このコマンドレットは実行されません。

```yaml
Type: System.Management.Automation.SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### 共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。 詳細については、「about_CommonParameters」(http://go.microsoft.com/fwlink/?LinkID=113216) を参照してください。
