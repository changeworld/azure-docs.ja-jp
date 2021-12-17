---
online version: https://github.com/Azure/sap-hana
schema: 2.0.0
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 10/21/2021
ms.topic: reference
ms.service: virtual-machines-sap
title: install_deployer.sh
description: シェル スクリプトを使用して、コントロール プレーンで新しい配置機能をブートストラップします。
ms.openlocfilehash: 5b9bea75379c1dd99917424eff23b976291c073b
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725163"
---
# <a name="install_deployersh"></a>install_deployer.sh

## <a name="synopsis"></a>概要
`install_deployer.sh` スクリプトを使用すると、コントロール プレーンに新しい配置機能 VM をセットアップできます。

## <a name="syntax"></a>構文

```bash
install_deployer.sh [ --parameterfile ] <String> 
[-i | --auto-approve]
```

## <a name="description"></a>説明
`install_deployer.sh` スクリプトを実行すると、コントロール プレーンに新しい配置機能 VM がセットアップされます。

配置機能 VM には、Ansible と Terraform がインストールされています。 SAP アーティファクトをデプロイするには、配置機能 VM を使用します。


## <a name="examples"></a>例

### <a name="example-1"></a>例 1

```bash
install_deployer.sh --parameterfile MGMT-WEEU-DEP00-INFRASTRUCTURE.tfvars
```

## <a name="parameters"></a>パラメーター

### `--parameterfile`
配置機能 VM のパラメーター ファイルを設定します。 詳細については、[コントロール プレーンの構成](../automation-configure-control-plane.md#deployer)に関するページを参照してください。

```yaml
Type: String
Aliases: `-p`

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
