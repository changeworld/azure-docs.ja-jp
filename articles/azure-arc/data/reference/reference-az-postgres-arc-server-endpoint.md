---
title: az postgres arc-server endpoint リファレンス
titleSuffix: Azure Arc-enabled data services
description: az postgres arc-server endpoint コマンドのリファレンス記事です。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 78111c030c1641cab22664d889e26505de879cf7
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131847700"
---
# <a name="az-postgres-arc-server-endpoint"></a>az postgres arc-server endpoint
## <a name="commands"></a>コマンド
| コマンド | 説明|
| --- | --- |
[az postgres arc-server endpoint の一覧](#az-postgres-arc-server-endpoint-list) | Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのエンドポイントを一覧表示します。
## <a name="az-postgres-arc-server-endpoint-list"></a>az postgres arc-server endpoint の一覧
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのエンドポイントを一覧表示します。
```bash
az postgres arc-server endpoint list 
```
### <a name="examples"></a>例
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのエンドポイントを一覧表示します。
```bash
az postgres arc-server endpoint list --name postgres01  --k8s-namespace namespace --use-k8s
```
### <a name="global-arguments"></a>グローバル引数
#### `--debug`
すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。
#### `--help -h`
このヘルプ メッセージを表示して終了します。
#### `--output -o`
出力形式。  使用できる値: json、jsonc、table、tsv。  既定値: json。
#### `--query -q`
JMESPath クエリ文字列。 詳細と例については、[http://jmespath.org/](http://jmespath.org) を参照してください。
#### `--verbose`
ログの詳細レベルを上げます。 完全なデバッグ ログを表示するには `--debug` を使用します。
