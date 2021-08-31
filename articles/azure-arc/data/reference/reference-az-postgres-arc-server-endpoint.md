---
title: az postgres arc-server endpoint リファレンス
titleSuffix: Azure Arc-enabled data services
description: az postgres arc-server endpoint コマンドのリファレンス記事です。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 8b3bf65f4e38177712f6deb333ba5d4994542b2f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780874"
---
# <a name="az-postgres-arc-server-endpoint"></a>az postgres arc-server endpoint
## <a name="commands"></a>コマンド
| command | 説明|
| --- | --- |
[az postgres arc-server endpoint の一覧](#az-postgres-arc-server-endpoint-list) | Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのエンドポイントを一覧表示します。
## <a name="az-postgres-arc-server-endpoint-list"></a>az postgres arc-server endpoint の一覧
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのエンドポイントを一覧表示します。
```bash
az postgres arc-server endpoint list [--name -n] 
                                     [--k8s-namespace -k]  
                                     
[--use-k8s]
```
### <a name="examples"></a>例
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのエンドポイントを一覧表示します。
```bash
az postgres arc-server endpoint list --name postgres01  --k8s-namespace namespace --use-k8s
```
### <a name="optional-parameters"></a>省略可能のパラメーター
#### `--name -n`
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの名前。
#### `--k8s-namespace -k`
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループがデプロイされる Kubernetes 名前空間。 名前空間が指定されていない場合は、kubeconfig で定義されている名前空間が使用されます。
#### `--use-k8s`
このアクションを実行するには、ローカル Kubernetes API を使用します。
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
