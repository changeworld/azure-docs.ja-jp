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
title: New-SAPAutomationRegion
description: コントロール プレーン (Deployer、SAP ライブラリ) をデプロイします
ms.openlocfilehash: 9f39ae06acb0aa90e780faf6f14d2dea2ff6cd72
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725390"
---
# New-SAPAutomationRegion

## 概要
この PowerShell コマンド `New-SAPAutomationRegion` は、Deployer VM、Azure Key Vault、SAP ライブラリなどのコントロール プレーンをデプロイします。

Deployer VM には、Ansible と Terraform のインストールがあります。 この VM は、SAP アーティファクトをデプロイします。

## 構文

```
New-SAPAutomationRegion [-DeployerParameterfile] <String> [-LibraryParameterfile] <String>
 [[-Subscription] <String>] [[-SPN_id] <String>] [[-SPN_password] <String>] [[-Tenant_id] <String>]
 [[-Vault] <String>] [[-StorageAccountName] <String>] [-Force] [-Silent] [<CommonParameters>]
```

## 説明
Deployer VM と SAP ライブラリを含むコントロール プレーンをデプロイします。 詳細については、[コントロール プレーンの構成](../automation-configure-control-plane.md)と[コントロール プレーンのデプロイ](../automation-deploy-control-plane.md)に関するページを参照してください。

## 例

### 例 1

この例では、パラメーター ファイルで定義されているコントロール プレーンをデプロイします。 このプロセスでは、SPN の詳細を求めるメッセージが表示されます。

```powershell
Import-Module "SAPDeploymentUtilities.psd1"

New-SAPAutomationRegion -DeployerParameterfile .\DEPLOYER\MGMT-WEEU-DEP01-INFRASTRUCTURE\MGMT-WEEU-DEP01-INFRASTRUCTURE.tfvars 
 -LibraryParameterfile .\LIBRARY\MGMT-WEEU-SAP_LIBRARY\MGMT-WEEU-SAP_LIBRARY.tfvars
```

### 例 2

この例では、パラメーター ファイルで定義されているコントロール プレーンをデプロイします。 このプロセスでは、デプロイのキー コンテナーにデプロイ資格情報を追加します。

```powershell
Import-Module "SAPDeploymentUtilities.psd1"

$Subscription=<subscriptionID>
$SPN_id=<appID>
$SPN_password=<password>
$Tenant_id=<tenant>

New-SAPAutomationRegion -DeployerParameterfile .\DEPLOYER\MGMT-WEEU-DEP01-INFRASTRUCTURE\MGMT-WEEU-DEP01-INFRASTRUCTURE.tfvars 
-LibraryParameterfile .\LIBRARY\MGMT-WEEU-SAP_LIBRARY\MGMT-WEEU-SAP_LIBRARY.tfvars
-Subscription $Subscription
-SPN_id $SPN_id
-SPN_password $SPN_password
-Tenant_id $Tenant_id
```

## パラメーター

### `-DeployerParameterfile`
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

### `-Subscription`
ターゲット Azure サブスクリプションを設定します。

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

### `-SPN_id`
サービス プリンシパルのアプリ ID を設定します。 詳細については、[デプロイ資格情報の準備](../automation-deploy-control-plane.md#prepare-the-deployment-credentials)に関する記事を参照してください。

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

### `-SPN_password`
サービス プリンシパルのパスワードを設定します。 詳細については、[デプロイ資格情報の準備](../automation-deploy-control-plane.md#prepare-the-deployment-credentials)に関する記事を参照してください。 

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

### `-Tenant_id`
サービス プリンシパルのテナント ID を設定します。 詳細については、[デプロイ資格情報の準備](../automation-deploy-control-plane.md#prepare-the-deployment-credentials)に関する記事を参照してください。 

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

### `-Vault`
デプロイのキー コンテナーの名前を設定します。

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

### `-StorageAccountName`
Terraform 状態ファイルを含むストレージ アカウントの名前を設定します。

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
サイレント デプロイを有効にします。

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

### 共通パラメーター
このコマンドレットでは、共通パラメーター (`-Debug`、`-ErrorAction`、`-ErrorVariable`、`-InformationAction`、`-InformationVariable`、`-OutVariable`、`-OutBuffer`、`-PipelineVariable`、`-Verbose`、`-WarningAction`、`-WarningVariable`) がサポートされます。 詳細については、「[about_CommonParameters](https://go.microsoft.com/fwlink/?LinkID=113216)」を参照してください。
## Notes
v0.9 - 初期バージョン

Copyright (c) Microsoft Corporation.
MIT ライセンスにより許諾されています。

## 関連リンク

+[GitHub リポジトリ: SAP デプロイ自動化フレームワーク](https://github.com/Azure/sap-hana)
