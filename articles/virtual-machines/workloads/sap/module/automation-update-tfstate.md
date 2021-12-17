---
external help file: SAPDeploymentUtilities-help.xml
Module Name: SAPDeploymentUtilities
online version: https://github.com/Azure/sap-hana
schema: 2.0.0
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 10/21/2021
ms.topic: reference
ms.service: virtual-machines-sap
title: Update-TFState
description: Terraform 状態ファイルを更新します
ms.openlocfilehash: f20c04a79364230f8918a08b97e58e4a881b2a0f
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725243"
---
# Update-TFState

## 概要
Terraform 状態ファイルを更新します。

## 構文

```powershell

Import-Module "SAPDeploymentUtilities.psd1"

Update-TFState [-Parameterfile] <String> [-Type] <SAP_Types> [-TerraformStateFileName] <String>
 [-Subscription] <String> [-StorageAccountName] <String> [-TerraformResourceName] <String>
 [-AzureResourceID] <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

## 説明
このコマンドレットを使用して、不足しているまたは変更されたリソースを Terraform 状態ファイルに追加できます。

## 例

### 例 1

```powershell

Import-Module "SAPDeploymentUtilities.psd1"

Update-TFState -Parameterfile .\DEV-WEEU-SAP01-X00.tfvars -Type sap_system
 -Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx 
 -StorageAccountName devweeutfstate### 
 -TerraformStateFileName DEV-WEEU-SAP01-X00.terraform.tfstate 
 -TerraformResourceName module.sap_system.azurerm_resource_group.deployer[0] 
 -AzureResourceID /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/DEV-WEEU-SAP01-X00
```

## パラメーター

### `-Parameterfile`
システムのパラメーター ファイルを設定します。

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### `-Type`
システムの種類を設定します。 有効な値は、`sap_deployer`、`sap_library`、`sap_landscape`、`sap_system` です。

```yaml
Type: SAP_Types
Parameter Sets: (All)
Aliases:
Accepted values: sap_deployer, sap_landscape, sap_library, sap_system

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### `-TerraformStateFileName`
Terraform 状態ファイルの名前を設定します。

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### `-Subscription`
Terraform 状態を含む Azure サブスクリプションを設定します。

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### `-StorageAccountName`
Terraform 状態ファイルのストレージ アカウント名を設定します。

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 5
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### `-TerraformResourceName`
Terraform 状態ファイルのリソース名を設定します。

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 6
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### `-AzureResourceID`
インポートする Azure リソースのリソース ID を設定します。

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 7
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### `-WhatIf`
コマンドレットが実行された場合の動作を示します。 ただし、コマンドレットでは何も変更されません。

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### `-Confirm`
コマンドレットの実行前に確認を求めるメッセージが表示されます。

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### 共通パラメーター
このコマンドレットでは、共通パラメーター (`-Debug`、`-ErrorAction`、`-ErrorVariable`、`-InformationAction`、`-InformationVariable`、`-OutVariable`、`-OutBuffer`、`-PipelineVariable`、`-Verbose`、`-WarningAction`、`-WarningVariable`) がサポートされます。 詳細については、「[about_CommonParameters](https://go.microsoft.com/fwlink/?LinkID=113216)」を参照してください。

## Notes
v0.9 - 初期バージョン


Copyright (c) Microsoft Corporation.
MIT ライセンスにより許諾されています。

## 関連リンク

[GitHub リポジトリ: SAP デプロイ自動化フレームワーク](https://github.com/Azure/sap-hana)
