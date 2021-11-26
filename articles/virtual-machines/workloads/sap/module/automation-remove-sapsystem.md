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
title: Remove-SAPSystem
description: SAP デプロイを削除します
ms.openlocfilehash: dd0f86e35c0530103b7df882d0bb89008ccf3ae0
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725245"
---
# Remove-SAPSystem

## 概要
SAP デプロイを削除します

## 構文

パラメーター ファイルで定義されている SAP デプロイを削除します。

```powershell
Import-Module "SAPDeploymentUtilities.psd1"

Remove-SAPSystem [-Parameterfile] <String> [-Type] <String> [<CommonParameters>]
```

## 説明
パラメーター ファイルで定義されている SAP デプロイを削除します。

## 例

### 例 1

SAP システムを削除します。

```powershell

Import-Module "SAPDeploymentUtilities.psd1"

Remove-System -Parameterfile .\PROD-WEEU-SAP00-X00.tfvars -Type sap_system
```

### 例 2

SAP ライブラリを削除します。

```powershell
Import-Module "SAPDeploymentUtilities.psd1"

Remove-System -Parameterfile .\PROD-WEEU-SAP_LIBRARY.tfvars -Type sap_library
```

## パラメーター

### `-Parameterfile`
SAP システムのパラメーター ファイルを設定します。

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
SAP システムの種類を設定します。

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
