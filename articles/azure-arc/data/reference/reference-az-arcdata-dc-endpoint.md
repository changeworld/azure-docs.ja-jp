---
title: az arcdata dc endpoint リファレンス
titleSuffix: Azure Arc-enabled data services
description: az arcdata dc endpoint コマンドのリファレンス記事。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: c024050777d01bb93fbbcba63a636d4e34c163b2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778944"
---
# <a name="az-arcdata-dc-endpoint"></a>az arcdata dc endpoint
## <a name="commands"></a>コマンド
| command | 説明|
| --- | --- |
[az arcdata dc endpoint list](#az-arcdata-dc-endpoint-list) | データ コントローラーのエンドポイントを一覧表示します。
## <a name="az-arcdata-dc-endpoint-list"></a>az arcdata dc endpoint list
データ コントローラーのエンドポイントを一覧表示します。
```bash
az arcdata dc endpoint list --k8s-namespace -k 
                            [--endpoint-name -e]  
                            
[--use-k8s]
```
### <a name="examples"></a>使用例
使用可能なすべてのデータ コントローラー エンドポイントを一覧表示します。
```bash
az arcdata dc endpoint list --k8s-namespace namespace
```
### <a name="required-parameters"></a>必須のパラメーター
#### `--k8s-namespace -k`
データ コントローラーが存在している Kubernetes 名前空間。
### <a name="optional-parameters"></a>省略可能のパラメーター
#### `--endpoint-name -e`
Arc データ コントローラーのエンドポイント名。
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
