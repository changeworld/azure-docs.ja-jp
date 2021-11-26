---
online version: https://github.com/Azure/sap-hana
schema: 2.0.0
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 10/21/2021
ms.topic: reference
ms.service: virtual-machines-sap
title: remover.sh
description: シェル スクリプトを使用して新しい SAP システムを削除します。
ms.openlocfilehash: a6d99d966a6be9ede32d26bef3757038260b0579
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725208"
---
# <a name="removersh"></a>Remover.sh

## <a name="synopsis"></a>概要
新しい SAP システムを削除するには、コマンド `remover.sh` を使用します。 このスクリプトは、ワークロード ゾーンと SAP システムを削除するために使用できます。

## <a name="syntax"></a>構文

```bash

remover.sh [--parameterfile] <String> [--type] <String> [ --help ]<String>]
s>]
```

## <a name="description"></a>説明
`remover.sh` スクリプトでは、指定された種類の新しいデプロイをデプロイまたは更新します。

## <a name="examples"></a>例

### <a name="example-1"></a>例 1

SAP システムのデプロイを削除します。

```bash

remover.sh --parameterfile DEV-WEEU-SAP00-X00.tfvars --type sap_system
```

### <a name="example-2"></a>例 2

ワークロードのデプロイを削除します。

```bash
remover.sh --parameterfile DEV-WEEU-SAP00-INFRASTRUCTURE.tfvars --type sap_landscape
```

## <a name="parameters"></a>パラメーター

### `--parameter_file`
システムのパラメーター ファイルを設定する

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
