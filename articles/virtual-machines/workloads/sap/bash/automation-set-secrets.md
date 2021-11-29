---
online version: https://github.com/Azure/sap-hana
schema: 2.0.0
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 10/21/2021
ms.topic: reference
ms.service: virtual-machines-sap
title: set_secrets.sh
description: シェル スクリプトを使用して、Azure Key Vault に SPN シークレットを設定します。
ms.openlocfilehash: 7f1c6102648aef85dbcb69c5adbdb4c2eeddb490
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132733863"
---
# <a name="set_secretssh"></a>set_secrets.sh

## <a name="synopsis"></a>概要
Azure Key Vault にサービス プリンシパルのシークレットを設定します。

## <a name="syntax"></a>構文

```bash

set_secrets.sh [--region] <String> [--environment] <String> [--vault] <String> [--subscription] <String> [--spn_id] <String>
 [--spn_secret] <String> [--tenant_id] <String>
```

## <a name="description"></a>説明
デプロイの自動化に必要なシークレットを Key Vault に設定します。

## EXAMPLES

### <a name="example-1"></a>例 1

```bash

set_secrets.sh --environment DEV                        \
    --region weeu                                       \
    --vault MGMTWEEUDEP00userABC                        \
    --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --spn_id yyyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyyy       \
    --spn_secret ************************               \
    --tenant_id zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz     
```

## <a name="parameters"></a>パラメーター

### `--region`
デプロイ用の Azure リージョンの名前を設定します。

```yaml
Type: String
Aliases: `-r`

Required: True
```

### `--environment`
デプロイ環境の名前を設定します。

```yaml
Type: String
```yaml
Type: String
Aliases: `-e`

Required: True
```

### `--vault`
キー コンテナーの名前を設定します。

```yaml
Type: String
Aliases: `-v`

Required: True
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

## <a name="notes"></a>Notes
v0.9 - 初期バージョン


Copyright (c) Microsoft Corporation.
MIT ライセンスにより許諾されています。
## <a name="related-links"></a>関連リンク

[GitHub リポジトリ: SAP デプロイ自動化フレームワーク](https://github.com/Azure/sap-hana)
