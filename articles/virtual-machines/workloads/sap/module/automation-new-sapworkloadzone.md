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
title: New-SAPWorkloadZone
description: 新しい SAP ワークロード ゾーンをデプロイする
ms.openlocfilehash: 7bceaa49a4823eaa61f2dcb76e7ffbb426cd8243
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725247"
---
# New-SAPWorkloadZone

## 概要
`New-SAPWorkloadZone` コマンドを使用して新しい SAP ワークロード ゾーンをデプロイできます。

## 構文

```powershell
Import-Module "SAPDeploymentUtilities.psd1"


New-SAPWorkloadZone [-Parameterfile] <String> [[-Deployerstatefile] <String>] [[-Deployerenvironment] <String>]
 [[-State_subscription] <String>] [[-Vault] <String>] [[-StorageAccountName] <String>]
 [[-Subscription] <String>] [[-SPN_id] <String>] [[-SPN_password] <String>] [[-Tenant_id] <String>] [-Force]
 [-Silent] [-WhatIf] [-Confirm] [<CommonParameters>]
```

## 説明
`New-SAPWorkloadZone` コマンドを使用すると、新しい SAP ワークロード ゾーンがデプロイされます。 ワークロード ゾーンには、内部のすべての VM の共有リソースが含まれています。

## 例

### 例 1

この例では、パラメーター ファイルで定義されているワークロード ゾーンをデプロイします。 このプロセスでは、SPN の詳細を求めるメッセージが表示されます。

```powershell

Import-Module "SAPDeploymentUtilities.psd1"

New-SAPWorkloadZone -Parameterfile .\PROD-WEEU-SAP00-infrastructure.tfvars
```

### 例 2

この例では、パラメーター ファイルで定義されているワークロード ゾーンをデプロイします。 このプロセスでは、デプロイのキー コンテナーにデプロイ資格情報を追加します。


```powershell
Import-Module "SAPDeploymentUtilities.psd1"

New-SAPWorkloadZone -Parameterfile .\PROD-WEEU-SAP00-infrastructure.tfvars
 -Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
 -SPN_id yyyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyyy
 -SPN_password ************************
 -Tenant_id zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz
```

## パラメーター

### `-Parameterfile`
ワークロード ゾーンのパラメーター ファイルを設定します。 詳細については、[ワークロード ゾーンの構成](../automation-configure-workload-zone.md)に関するページを参照してください。

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

### `-Deployerstatefile`
Deployer VM の Terraform 状態ファイル名を設定します。

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### `-Deployerenvironment`
Deployer の環境名

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### `-State_subscription`
Terraform ストレージ アカウントのサブスクリプション ID を設定します。

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### `-Vault`
デプロイ資格情報のキー コンテナーを設定します。

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 5
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### `-StorageAccountName`
ストレージ アカウント名

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 6
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### `-Subscription`
ターゲット サブスクリプションを設定します。

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 7
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### `-SPN_id`
サービス プリンシパルのアプリ ID を設定します。 詳細については、[デプロイ資格情報の準備](../automation-deploy-workload-zone.md#preparing-the-workload-zone-deployment-credentials)に関する記事を参照してください。
```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 8
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### `-SPN_password`
サービス プリンシパルのパスワードを設定します。 詳細については、[デプロイ資格情報の準備](../automation-deploy-workload-zone.md#preparing-the-workload-zone-deployment-credentials)に関する記事を参照してください。

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 9
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### `-Tenant_id`
テナント ID を設定します。 詳細については、[デプロイ資格情報の準備](../automation-deploy-workload-zone.md#preparing-the-workload-zone-deployment-credentials)に関する記事を参照してください。

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 10
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### `-Force`
ローカル構成をクリーン アップします。

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

### `-Silent`
プロンプトを表示せずにデプロイします。

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
