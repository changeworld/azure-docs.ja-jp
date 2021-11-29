---
online version: https://github.com/Azure/sap-hana
schema: 2.0.0
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 10/21/2021
ms.topic: reference
ms.service: virtual-machines-sap
title: update_sas_token.sh
description: Azure Key Vault の SAP ライブラリ SAS トークンを更新します
ms.openlocfilehash: 09b9506c54e2595679d5fcd7189548453472d6a1
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132733862"
---
# <a name="update_sas_tokensh"></a>update_sas_token.sh

## <a name="synopsis"></a>概要
Azure Key Vault の SAP ライブラリ SAS トークンを更新します

## <a name="syntax"></a>構文

```bash

update_sas_token.sh
```

## <a name="description"></a>説明
Azure Key Vault の SAP ライブラリ SAS トークンを更新します。 SAP ライブラリ ストレージ アカウント名と配置機能のキー コンテナー名の入力を求めます。

## EXAMPLES

### <a name="example-1"></a>例 1

SAP ライブラリ ストレージ アカウント名と配置機能のキー コンテナー名の入力を求めます。

```bash

update_sas_token.sh
```

### <a name="example-2"></a>例 2

```bash

export SAP_LIBRARY_TF=mgmtweeusaplibXXX
export SAP_KV_TF=MGMTWEEUDEP00userYYY

update_sas_token.sh
```


## <a name="parameters"></a>パラメーター

なし

## <a name="notes"></a>Notes
v0.9 - 初期バージョン


Copyright (c) Microsoft Corporation.
MIT ライセンスにより許諾されています。
## <a name="related-links"></a>関連リンク

[GitHub リポジトリ: SAP デプロイ自動化フレームワーク](https://github.com/Azure/sap-hana)
