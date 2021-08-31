---
title: az arcdata dc status のレファレンス
titleSuffix: Azure Arc-enabled data services
description: az arcdata dc status コマンドのリファレンス記事です。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 9e795c71a5316960bfc7f0b1138afce3ecc4b957
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779082"
---
# <a name="az-arcdata-dc-status"></a>az arcdata dc status
## <a name="commands"></a>コマンド
| command | 説明|
| --- | --- |
[az arcdata dc status show](#az-arcdata-dc-status-show) | データ コントローラーの状態を表示します。
## <a name="az-arcdata-dc-status-show"></a>az arcdata dc status show
データ コントローラーの状態を表示します。
```bash
az arcdata dc status show [--k8s-namespace -k] 
                          [--use-k8s]
```
### <a name="examples"></a>例
特定の kubernetes 名前空間のデータ コントローラーの状態を表示します。
```bash
az arcdata dc status show --k8s-namespace <ns>
```
### <a name="optional-parameters"></a>省略可能のパラメーター
#### `--k8s-namespace -k`
データ コントローラーが存在している Kubernetes 名前空間。
#### `--use-k8s`
このアクションを実行するには、ローカルの Kubernetes API を使用します。
### <a name="global-arguments"></a>グローバル引数
#### `--debug`
すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。
#### `--help -h`
このヘルプ メッセージを表示して終了します。
#### `--output -o`
出力形式。  json、jsonc、none、table、tsv、yaml、yamlc のいずれかの値を指定できます。  既定値: json。
#### `--query -q`
JMESPath クエリ文字列。 詳細と例については、[http://jmespath.org](http://jmespath.org) を参照してください。
#### `--subscription`
サブスクリプションの名前または ID。 `az account set -s NAME_OR_ID` を使用して、既定のサブスクリプションを構成できます。
#### `--verbose`
ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。
