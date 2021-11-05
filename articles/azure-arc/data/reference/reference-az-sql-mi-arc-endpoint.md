---
title: az sql mi-arc endpoint リファレンス
titleSuffix: Azure Arc-enabled data services
description: az sql mi-arc endpoint コマンドのリファレンス記事です。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: db7c1fb6366cda40a5978a7be55100ae7a12ac4a
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852404"
---
# <a name="az-sql-mi-arc-endpoint"></a>az sql mi-arc endpoint
## <a name="commands"></a>コマンド
| コマンド | 説明|
| --- | --- |
[az sql mi-arc endpoint list](#az-sql-mi-arc-endpoint-list) | SQL エンドポイントを一覧表示します。
## <a name="az-sql-mi-arc-endpoint-list"></a>az sql mi-arc endpoint list
SQL エンドポイントを一覧表示します。
```bash
az sql mi-arc endpoint list 
```
### <a name="examples"></a>例
SQL マネージド インスタンスのエンドポイントを一覧表示します。
```bash
az sql mi-arc endpoint list -n sqlmi1
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
