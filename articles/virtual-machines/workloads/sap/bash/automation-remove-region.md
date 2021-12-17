---
online version: https://github.com/Azure/sap-hana
schema: 2.0.0
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 10/21/2021
ms.topic: reference
ms.service: virtual-machines-sap
title: Remove_region.sh
description: シェル スクリプトを使用して SAP コントロール プレーン (デプロイ機能、ライブラリ) を削除します。
ms.openlocfilehash: 29f7461208120ee7d88c44943b7c393a0ca47ebf
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132733867"
---
# <a name="remove_regionsh"></a>Remove_region.sh

## <a name="synopsis"></a>概要

デプロイ機能 VM と SAP ライブラリを含むコントロール プレーンを削除します。
## <a name="syntax"></a>構文

```bash

Remove_region.sh  [-d or --deployer_parameter_file ] <String> [-l or --library_parameter_file ] <String>
```

## <a name="description"></a>説明
デプロイ機能 VM と SAP ライブラリを含む SAP コントロール プレーンを削除します。

## <a name="examples"></a>例

### <a name="example-1"></a>例 1
```bash
${DEPLOYMENT_REPO_PATH}/deploy/scripts/prepare_region.sh                                                         \
        --deployer_parameter_file DEPLOYER/MGMT-WEEU-DEP00-INFRASTRUCTURE/MGMT-WEEU-DEP00-INFRASTRUCTURE.tfvars  \
        --library_parameter_file LIBRARY/MGMT-WEEU-SAP_LIBRARY/MGMT-WEEU-SAP_LIBRARY.tfvars                      
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
