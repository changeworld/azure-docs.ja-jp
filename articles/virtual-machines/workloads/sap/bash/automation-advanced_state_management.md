---
online version: https://github.com/Azure/sap-hana
schema: 2.0.0
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 10/21/2021
ms.topic: reference
ms.service: virtual-machines-sap
title: advanced_state_management
description: シェル スクリプトを使用して Terraform 状態ファイルを更新する
ms.openlocfilehash: 40a7810227802415fa69578936f00d7715d40191
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725155"
---
# <a name="advanced_state_managementsh"></a>advanced_state_management.sh

## <a name="synopsis"></a>概要
Terraform 状態ファイルを更新します。

## <a name="syntax"></a>構文

```bash

advanced_state_management.sh [--parameterfile] <String> 
[--type] <String> 
[--terraform_keyfile] <String>
[--subscription] <String> 
[--storage_account_name] <String> 
[--tf_resource_name] <String>
[--azure_resource_id] <String> 
[--help]
```

## <a name="description"></a>説明
このコマンドレットを使用して、不足しているまたは変更されたリソースを Terraform 状態ファイルに追加できます。

## <a name="examples"></a>例

### <a name="example-1"></a>例 1

```bash

advanced_state_management.sh --parameterfile DEV-WEEU-SAP01-X00.tfvars --type sap_system
 --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx 
 --storage_account_name devweeutfstateABC 
 --terraform_keyfile DEV-WEEU-SAP01-X00.terraform.tfstate 
 --tf_resource_name module.sap_system.azurerm_resource_group.deployer[0] 
 --azure_resource_id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/DEV-WEEU-SAP01-X00
```

## <a name="parameters"></a>パラメーター

### `--parameterfile`
システムのパラメーター ファイルを設定します。

```yaml
Type: String
Aliases: `-p`

Required: True
```

### `--type`
システムの種類を設定します。 有効な値は、`sap_deployer`、`sap_library`、`sap_landscape`、`sap_system` です。

```yaml
Type: String
Aliases: `-t`
Accepted values: sap_deployer, sap_landscape, sap_library, sap_system

Required: True
```

### `--terraform_keyfile`
Terraform 状態ファイルの名前を設定します。

```yaml
Type: String
Aliases: `-k`

Required: True
```

### `--subscription`
ターゲット Azure サブスクリプションを設定します。

```yaml
Type: String
Aliases: `-s`

Required: False
```

### `--storageaccountname`
Terraform 状態ファイルを含むストレージ アカウントの名前を設定します。

```yaml
Type: String
Aliases: `-a`

Required: False
```

### `--tf_resource_name`
Terraform 状態ファイルのリソース名を設定します。

```yaml
Type: String
Aliases: `-n`

Required: False
```

### `--azure_resource_id`
インポートする Azure リソースのリソース ID を設定します。

```yaml
Type: String
Aliases: `-i`

Required: False
```

## <a name="notes"></a>Notes
v0.9 - 初期バージョン


Copyright (c) Microsoft Corporation.
MIT ライセンスにより許諾されています。

## <a name="related-links"></a>関連リンク

[GitHub リポジトリ: SAP デプロイ自動化フレームワーク](https://github.com/Azure/sap-hana)
