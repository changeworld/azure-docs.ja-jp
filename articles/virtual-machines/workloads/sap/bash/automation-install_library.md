---
online version: https://github.com/Azure/sap-hana
schema: 2.0.0
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 10/21/2021
ms.topic: reference
ms.service: virtual-machines-sap
title: install_library.sh
description: シェル スクリプトを使用して、コントロール プレーンで新しい SAP ライブラリをブートストラップします。
ms.openlocfilehash: 0fadaace80e1b349484c29bd115c4f79e3295530
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725157"
---
# <a name="install_librarysh"></a>Install_library.sh

## <a name="synopsis"></a>概要
`install_library.sh` スクリプトを使用すると、新しい SAP ライブラリが設定されます。

## <a name="syntax"></a>構文

```bash
install_library.sh [ --parameterfile ] <String> [ --deployer_statefile_foldername ] <String> 
[-i | --auto-approve]
```

## <a name="description"></a>説明
`install_library.sh` コマンドを使用すると、コントロール プレーンに新しい SAP ライブラリが設定されます。
SAP ライブラリは、Terraform 状態ファイルと SAP インストール メディア用のストレージを提供します。

## <a name="examples"></a>例

### <a name="example-1"></a>例 1
```bash

install_library.sh -Parameterfile MGMT-WEEU-SAP_LIBRARY.tfvars -deployer_statefile_foldername ../../DEPLOYER/MGMT-WEEU-DEP00-INFRASTRUCTURE
```

## <a name="parameters"></a>パラメーター

### `--parameterfile`
Deployer VM のパラメーター ファイルを設定します。 詳細については、[コントロール プレーンの構成](../automation-configure-control-plane.md#deployer)に関するページを参照してください。

```yaml
Type: String
Aliases: `-p`

Required: True
```

### `--deployer_statefile_foldername`
`terraform.tfstate` という名前の Deployer VM のパラメーター ファイルを含むフォルダーへの相対フォルダー パスを設定します。

```yaml
Type: String
Aliases: `-d`

Required: True
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
