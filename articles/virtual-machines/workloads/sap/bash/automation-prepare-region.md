---
online version: https://github.com/Azure/sap-hana
schema: 2.0.0
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 10/21/2021
ms.topic: reference
ms.service: virtual-machines-sap
title: リージョンを準備する
description: シェル スクリプトを使用してコントロール プレーン (デプロイ機能、SAP ライブラリ) をデプロイします。
ms.openlocfilehash: 2510e8a289047e21364e16c147290fa0ef9d4475
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132733869"
---
# <a name="prepare_regionsh"></a>prepare_region.sh

## <a name="synopsis"></a>概要
この `prepare_region.sh` スクリプトは、デプロイ機能 VM、Azure Key Vault、SAP ライブラリなどのコントロール プレーンをデプロイします。

デプロイ機能 VM には、Ansible と Terraform のインストールがあります。 この VM は、SAP アーティファクトをデプロイします。

## <a name="syntax"></a>構文

```bash
prepare_region.sh [ --deployer_parameter_file ] <String> [ --library_parameter_file ] <String>
 [[ --subscription] <String>] [[ --spn_id  ] <String>] [[ --spn_secret ] <String>] [[ --tenant_id ] <String>]
 [[ --storageaccountname] <String>] [ --force ] [ --auto-approve ]
```

## <a name="description"></a>説明
デプロイ機能 VM と SAP ライブラリを含むコントロール プレーンをデプロイします。 詳細については、[コントロール プレーンの構成](../automation-configure-control-plane.md)と[コントロール プレーンのデプロイ](../automation-deploy-control-plane.md)に関するページを参照してください。

## <a name="examples"></a>例

### <a name="example-1"></a>例 1

この例では、パラメーター ファイルで定義されているコントロール プレーンをデプロイします。 このプロセスでは、SPN の詳細を求めるメッセージが表示されます。

```bash
${DEPLOYMENT_REPO_PATH}/deploy/scripts/prepare_region.sh                                                         \
        --deployer_parameter_file DEPLOYER/MGMT-WEEU-DEP00-INFRASTRUCTURE/MGMT-WEEU-DEP00-INFRASTRUCTURE.tfvars  \
        --library_parameter_file LIBRARY/MGMT-WEEU-SAP_LIBRARY/MGMT-WEEU-SAP_LIBRARY.tfvars                      
```

### <a name="example-2"></a>例 2

この例では、パラメーター ファイルで定義されているコントロール プレーンをデプロイします。 このプロセスでは、デプロイのキー コンテナーにデプロイ資格情報を追加します。

```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES

az logout
az login

subscriptionID=<subscriptionID>
appId=<appID>
spn_secret=<password>
tenant_id=<tenant>

${DEPLOYMENT_REPO_PATH}/deploy/scripts/prepare_region.sh                                                         \
        --deployer_parameter_file DEPLOYER/MGMT-WEEU-DEP00-INFRASTRUCTURE/MGMT-WEEU-DEP00-INFRASTRUCTURE.tfvars  \
        --library_parameter_file LIBRARY/MGMT-WEEU-SAP_LIBRARY/MGMT-WEEU-SAP_LIBRARY.tfvars                      \
        --subscription $subscriptionID                                                                           \
        --spn_id $appID                                                                                          \
        --spn_secret "$spn_secret"                                                                               \ 
        --tenant_id $tenant
```

## <a name="parameters"></a>パラメーター

### `--deployer_parameter_file`
デプロイ機能 VM のパラメーター ファイルを設定します。 詳細については、[コントロール プレーンの構成](../automation-configure-control-plane.md#deployer)に関するページを参照してください。

```yaml
Type: String
Aliases: `-d`

Required: True
```

### `--library_parameter_file`
SAP ライブラリのパラメーター ファイルを設定します。 詳細については、[コントロール プレーンの構成](../automation-configure-control-plane.md#sap-library)に関するページを参照してください。

```yaml
Type: String
Aliases: `-l`

Required: True
```

### `--subscription`
ターゲット Azure サブスクリプションを設定します。

```yaml
Type: String
Aliases: `-s`

Required: False
```

### `-spn_id`
サービス プリンシパルのアプリ ID を設定します。 詳細については、[デプロイ資格情報の準備](../automation-deploy-control-plane.md#prepare-the-deployment-credentials)に関する記事を参照してください。

```yaml
Type: String
Aliases: `-c`

Required: False
```

### `--spn_secret`
サービス プリンシパルのパスワードを設定します。 詳細については、[デプロイ資格情報の準備](../automation-deploy-control-plane.md#prepare-the-deployment-credentials)に関する記事を参照してください。 

```yaml
Type: String
Aliases: `-p`

Required: False
```

### `--tenant_id`
サービス プリンシパルのテナント ID を設定します。 詳細については、[デプロイ資格情報の準備](../automation-deploy-control-plane.md#prepare-the-deployment-credentials)に関する記事を参照してください。 

```yaml
Type: String
Aliases: `-t`

Required: False
```


### `--storageaccountname`
Terraform 状態ファイルを含むストレージ アカウントの名前を設定します。

```yaml
Type: String
Aliases: `-a`

Required: False
```

### `--force`
ローカル構成をクリーン アップします。

```yaml
Type: SwitchParameter
Aliases: `-f`

Required: False
```

### `--auto-approve`
サイレント デプロイを有効にします。

```yaml
Type: SwitchParameter
Aliases: `-i`

Required: False
```

### `--help`
スクリプトのヘルプを表示します。

```yaml
Type: SwitchParameter
Aliases: `-h`

Required: False
```

## <a name="notes"></a>Notes
v0.9 - 初期バージョン

Copyright (c) Microsoft Corporation.
MIT ライセンスにより許諾されています。

## <a name="related-links"></a>関連リンク

+[GitHub リポジトリ: SAP デプロイ自動化フレームワーク](https://github.com/Azure/sap-hana)
