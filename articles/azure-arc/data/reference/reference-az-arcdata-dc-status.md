---
title: az arcdata dc status のレファレンス
titleSuffix: Azure Arc-enabled data services
description: az arcdata dc status コマンドのリファレンス記事です。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 542f0f9568150abf0646e1a494e14eec2be6e524
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131841638"
---
# <a name="az-arcdata-dc-status"></a>az arcdata dc status
## <a name="commands"></a>コマンド
| コマンド | 説明|
| --- | --- |
[az arcdata dc status show](#az-arcdata-dc-status-show) | データ コントローラーの状態を表示します。
## <a name="az-arcdata-dc-status-show"></a>az arcdata dc status show
データ コントローラーの状態を表示します。
```bash
az arcdata dc status show 
```
### <a name="examples"></a>例
特定の kubernetes 名前空間のデータ コントローラーの状態を表示します。
```bash
az arcdata dc status show --k8s-namespace namespace --use-k8s
```
特定のリソース グループでの直接接続されたデータ コントローラーの状態を表示します。
```bash
az arcdata dc status show --resource-group resource-group    
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
