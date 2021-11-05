---
title: az arcdata dc endpoint リファレンス
titleSuffix: Azure Arc-enabled data services
description: az arcdata dc endpoint コマンドのリファレンス記事。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 9d07d5e5263fd887af49552bb45eb98ae7f35441
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845743"
---
# <a name="az-arcdata-dc-endpoint"></a>az arcdata dc endpoint
## <a name="commands"></a>コマンド
| コマンド | 説明|
| --- | --- |
[az arcdata dc endpoint list](#az-arcdata-dc-endpoint-list) | データ コントローラーのエンドポイントを一覧表示します。
## <a name="az-arcdata-dc-endpoint-list"></a>az arcdata dc endpoint list
データ コントローラーのエンドポイントを一覧表示します。
```bash
az arcdata dc endpoint list 
```
### <a name="examples"></a>使用例
使用可能なすべてのデータ コントローラー エンドポイントを一覧表示します。
```bash
az arcdata dc endpoint list --k8s-namespace namespace
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
