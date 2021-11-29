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
title: Remove-SAPAutomationRegion
description: SAP コントロール プレーンを削除します (配置機能、ライブラリ)
ms.openlocfilehash: 3de052a6ef1ce5e3bacf82cfa7439af49547735a
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725246"
---
# Remove-SAPAutomationRegion

## 概要

配置機能 VM と SAP ライブラリを含むコントロール プレーンを削除します。
## 構文

```powershell
Import-Module "SAPDeploymentUtilities.psd1"

Remove-SAPAutomationRegion [-DeployerParameterfile] <String> [-LibraryParameterfile] <String>
 [<CommonParameters>]
```

## 説明
配置機能 VM と SAP ライブラリを含む SAP コントロール プレーンを削除します。

## 例

### 例 1
```powershell
Import-Module "SAPDeploymentUtilities.psd1"

Remove-SAPAutomationRegion -DeployerParameterfile .\DEPLOYER\MGMT-WEEU-SAP01-INFRASTRUCTURE\MGMT-WEEU-SAP01-INFRASTRUCTURE.tfvars 
 -LibraryParameterfile .\LIBRARY\MGMT-WEEU-SAP_LIBRARY\MGMT-WEEU-SAP_LIBRARY.tfvars

```

## パラメーター

### `-DeployerParameterfile`
配置機能 VM のパラメーター ファイルを設定します。 詳細については、[コントロール プレーンの構成](../automation-configure-control-plane.md#deployer)に関するページを参照してください。

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

### `-LibraryParameterfile`
SAP ライブラリのパラメーター ファイルを設定します。 詳細については、[コントロール プレーンの構成](../automation-configure-control-plane.md#sap-library)に関するページを参照してください。

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
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
