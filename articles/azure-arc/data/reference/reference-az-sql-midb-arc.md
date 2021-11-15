---
title: az sql midb-arc
titleSuffix: Azure Arc-enabled data services
description: az sql midb-arc コマンドのリファレンス記事です。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 8053ea28fe1ce1f7f05c1bb710265fb86839dcd2
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131858445"
---
# <a name="az-sql-midb-arc"></a>az sql midb-arc
## <a name="commands"></a>コマンド
| コマンド | 説明|
| --- | --- |
[az sql midb-arc restore](#az-sql-midb-arc-restore) | Azure Arc 対応 SQL マネージド インスタンスにデータベースを復元します。
## <a name="az-sql-midb-arc-restore"></a>az sql midb-arc restore
Azure Arc 対応 SQL マネージド インスタンスにデータベースを復元します。
```bash
az sql midb-arc restore 
```
### <a name="examples"></a>例
例 1 - ポイントインタイム リストアを使用してデータベースを復元します。
```bash
az sql midb-arc restore --managed-instance sqlmi1 --name mysourcedb
 --dest-name mynewdb --time "2021-10-20T05:34:22Z" --k8s-namespace
 arc --use-k8s --dry-run
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
