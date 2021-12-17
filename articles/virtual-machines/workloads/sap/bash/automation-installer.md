---
online version: https://github.com/Azure/sap-hana
schema: 2.0.0
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 10/21/2021
ms.topic: reference
ms.service: virtual-machines-sap
title: installer.sh
description: シェル スクリプトを使用して新しい SAP システムをデプロイします。
ms.openlocfilehash: ba189394a6bd4ae30c43f4dcbfcb6a1f9c4d0f17
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725154"
---
# <a name="installersh"></a>Installer.sh

## <a name="synopsis"></a>概要
新しい SAP システムをデプロイするには、`installer.sh` コマンドを使用します。 このスクリプトを使用して、さまざまな種類のデプロイを行うことができます。

## <a name="syntax"></a>構文

```bash

installer.sh [--parameterfile] <String> [--type] <String> [[--deployer_tfstate_key] <String>]
 [[ --landscape_tfstate_key] <String>] [[--storageaccountname] <String>] [[ --state_subscription ] <String>] [[ --state_subscription ] <String>] [[ --state_subscription ] [ --force ] [ --auto-approve ]<String>]
s>]
```

## <a name="description"></a>説明
`installer.sh` スクリプトでは、指定された種類の新しい SAP システムをデプロイまたは更新します。

## <a name="examples"></a>例

### <a name="example-1"></a>例 1

SAP システムをデプロイまたは更新します。

```bash

installer.sh --parameterfile DEV-WEEU-SAP00-X00.tfvars --type sap_system
```

### <a name="example-2"></a>例 2

SAP システムをデプロイまたは更新します。

```bash

installer.sh --parameterfile DEV-WEEU-SAP00-X00.tfvars --type sap_system \ 
--deployer_tfstate_key MGMT-WEEU-DEP00-INFRASTRUCTURE.terraform.tfstate  \
--landscape_tfstate_key DEV-WEEU-SAP01-INFRASTRUCTURE.terraform.tfstate
```

### <a name="example-3"></a>例 3

SAP ライブラリをデプロイまたは更新します。

```bash
installer.sh -Parameterfile MGMT-WEEU-SAP_LIBRARY.tfvars --type sap_library
```

## <a name="parameters"></a>パラメーター

### `--parameter_file`
システムのパラメーター ファイルを設定します。 詳細については、「[SAP システムを構成する](../automation-configure-system.md)」を参照してください。

```yaml
Type: String
Aliases: `-p`

Required: True
```

### `--type`
デプロイの種類を設定します。 有効な値は、`sap_deployer`、`sap_library`、`sap_landscape`、`sap_system` です。

```yaml
Type: String
Accepted values: sap_deployer, sap_landscape, sap_library, sap_system
Aliases: `-t`

Required: True
```

### <a name="--deployer_tfstate_key"></a>--deployer_tfstate_key
配置機能のデプロイの状態ファイルの名前を設定します。

```yaml
Type: String
Aliases: `-d`

Required: False
```

### <a name="-landscape_tfstate_key"></a>-landscape_tfstate_key
ワークロード ゾーンのデプロイの状態ファイルの名前を設定します。

```yaml
Type: String
Aliases: `-l`

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

### `--force`
ローカル構成をクリーンアップします。

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
