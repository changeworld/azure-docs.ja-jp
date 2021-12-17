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
title: New-SAPDeployer
description: コントロール プレーンで新しいDeployer をブートストラップします。
ms.openlocfilehash: 26c2510726f8d24337d92546c06ec33accfb056e
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725389"
---
# New-SAPDeployer

## 概要
コマンド `New-SAPDeployer` を使用すると、コントロール プレーンに新しい Deployer VM を設定できます。

## 構文

```powershell
New-SAPDeployer [-Parameterfile] <String> [-Silent] [-WhatIf] [-Confirm] [<CommonParameters>]
```

## 説明
コマンド `New-SAPDeployer` を使用すると、コントロール プレーンに新しい Deployer VM が設定されます。

Deployer VM には、Ansible と Terraform がインストールされています。 SAP アーティファクトをデプロイするには、Deployer VM を使用します。


## 例

### 例 1

```powershell
Import-Module "SAPDeploymentUtilities.psd1"

New-SAPDeployer -Parameterfile .\MGMT-WEEU-MGMT00-INFRASTRUCTURE.tfvars
```

## パラメーター

### `-Parameterfile`
Deployer VM のパラメーター ファイルを設定します。 詳細については、[コントロール プレーンの構成](../automation-configure-control-plane.md#deployer)に関するページを参照してください。

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

### -Silent
プロンプトを表示せずにデプロイする

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

### -WhatIf
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

### -Confirm
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
この `CommonParameters` コマンドレットでは、共通パラメーター (`-Debug`、`-ErrorAction`、`-ErrorVariable`、`-InformationAction`、`-InformationVariable`、`-OutVariable`、`-OutBuffer`、`-PipelineVariable`、`-Verbose`、`-WarningAction`、`-WarningVariable`) がサポートされます。 詳細については、「[about_CommonParameters](https://go.microsoft.com/fwlink/?LinkID=113216)」を参照してください。

## Notes
v0.9 - 初期バージョン

Copyright (c) Microsoft Corporation.
MIT ライセンスにより許諾されています。

## 関連リンク

[GitHub リポジトリ: SAP デプロイ自動化フレームワーク](https://github.com/Azure/sap-hana)
