---
online version: https://github.com/Azure/sap-hana
schema: 2.0.0
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 10/21/2021
ms.topic: reference
ms.service: virtual-machines-sap
title: install_workloadzone.sh
description: シェル スクリプトを使用して新しい SAP ワークロード ゾーンをデプロイします。
ms.openlocfilehash: b88f24ff1f70b3786e09f9285bd697ca3bf2aeaa
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725153"
---
# <a name="install_workloadzonesh"></a>install_workloadzone.sh

## <a name="synopsis"></a>概要
`install_workloadzone.sh` スクリプトを使用して新しい SAP ワークロード ゾーンをデプロイできます。

## <a name="syntax"></a>構文

```bash

install_workloadzone.sh [ -p or --parameterfile ] <String> 
 [[ --deployer_tfstate_key ] <String>] [[ --deployer_environment] <String>] [[ --state_subscription] <String>] [[ --storageaccountname ]
 [[ --subscription] <String>] [[ --spn_id  ] <String>] [[ --spn_secret ] <String>] [[ --tenant_id ] <String>]
 [[ --storageaccountname] <String>] [ force] [-i | --auto-approve]
```

## <a name="description"></a>説明
`install_workloadzone.sh` コマンドを使用すると、新しい SAP ワークロード ゾーンがデプロイされます。 ワークロード ゾーンには、すべての VM の共有リソースが含まれています。

## <a name="examples"></a>例

### <a name="example-1"></a>例 1

この例では、パラメーター ファイルで定義されているワークロード ゾーンをデプロイします。 このプロセスでは、SPN の詳細を求めるメッセージが表示されます。

```bash

install_workloadzone.sh -parameterfile PROD-WEEU-SAP00-infrastructure.tfvars
```

### <a name="example-2"></a>例 2

この例では、パラメーター ファイルで定義されているワークロード ゾーンをデプロイします。 このプロセスでは、デプロイのキー コンテナーにデプロイ資格情報を追加します。

```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/LANDSCAPE/DEV-WEEU-SAP01-INFRASTRUCTURE

subscriptionID=<subscriptionID>
appId=<appID>
spn_secret=<password>
tenant_id=<tenant>
keyvault=<keyvaultName>
storageaccount=<storageaccountName>
statefile_subscription=<statefile_subscription>

${DEPLOYMENT_REPO_PATH}/deploy/scripts/install_workloadzone.sh \
        --parameter_file DEV-WEEU-SAP01-INFRASTRUCTURE.tfvars  \
        --keyvault $keyvault                                   \
        --state_subscription $statefile_subscription           \
        --subscription $subscriptionID                         \
        --spn_id $appID                                        \
        --spn_secret "$spn_secret"                             \ 
        --tenant_id $tenant
```
## <a name="parameters"></a>パラメーター

### `--parameter_file`
ワークロード ゾーンのパラメーター ファイルを設定します。 詳細については、[ワークロード ゾーンの構成](../automation-configure-workload-zone.md)に関するページを参照してください。

```yaml
Type: String
Aliases: `-p`

Required: True
```

### `--deployer_tfstate_key`
Deployer VM の Terraform 状態ファイル名を設定します。

```yaml
Type: String
Aliases: `-d`

Required: False
```

### `deployer_environment`
Deployer の環境名

```yaml
Type: String
Aliases: `-e`

Required: False
```

### `--state_subscription`
Terraform ストレージ アカウントのサブスクリプション ID を設定します。

```yaml
Type: String
Aliases: `-k`

Required: False
```
### `--storageaccountname`
Terraform 状態ファイルを含むストレージ アカウントの名前を設定します。

```yaml
Type: String
Aliases: `-a`

Required: False
```

### `--keyvault`
デプロイ資格情報のキー コンテナーを設定します。

```yaml
Type: String
Aliases: `-v`

Required: False
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

[GitHub リポジトリ: SAP デプロイ自動化フレームワーク](https://github.com/Azure/sap-hana)
