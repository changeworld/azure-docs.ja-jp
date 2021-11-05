---
title: az arcdata dc config リファレンス
titleSuffix: Azure Arc-enabled data services
description: az arcdata dc config コマンドのリファレンス記事。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 72df8db00f1ab0366cc4daf8af8e5613645fc813
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852524"
---
# <a name="az-arcdata-dc-config"></a>az arcdata dc config
## <a name="commands"></a>コマンド
| コマンド | 説明|
| --- | --- |
[az arcdata dc config init](#az-arcdata-dc-config-init) | `az arcdata dc create` で使用できるデータ コントローラーの構成プロファイルを初期化します。
[az arcdata dc config list](#az-arcdata-dc-config-list) | 使用可能な構成プロファイルの選択肢の一覧を取得します。
[az arcdata dc config add](#az-arcdata-dc-config-add) | 構成ファイル内の json パスの値を追加します。
[az arcdata dc config remove](#az-arcdata-dc-config-remove) | 構成ファイル内の json パスの値を削除します。
[az arcdata dc config replace](#az-arcdata-dc-config-replace) | 構成ファイル内の json パスの値を置き換えます。
[az arcdata dc config patch](#az-arcdata-dc-config-patch) | json パッチ ファイルに基づいて、構成ファイルにパッチを適用します。
## <a name="az-arcdata-dc-config-init"></a>az arcdata dc config init
`az arcdata dc create` で使用できるデータ コントローラーの構成プロファイルを初期化します。 構成プロファイルの特定のソースを引数で指定できます。
```bash
az arcdata dc config init 
```
### <a name="examples"></a>例
ガイド付きのデータ コントローラー構成初期化エクスペリエンス - 必要な値の入力を求めるプロンプトが表示されます。
```bash
az arcdata dc config init
```
arcdata dc config init と引数で、./custom 内に aks-dev-test の構成プロファイルを作成します。
```bash
az arcdata dc config init --source azure-arc-kubeadm --path custom
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
## <a name="az-arcdata-dc-config-list"></a>az arcdata dc config list
`arcdata dc config init` で使用するために、使用可能な構成プロファイルの選択肢の一覧を取得します
```bash
az arcdata dc config list 
```
### <a name="examples"></a>例
使用可能なすべての構成プロファイル名を表示します。
```bash
az arcdata dc config list
```
特定の構成プロファイルの json を表示します。
```bash
az arcdata dc config list --config-profile aks-dev-test
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
## <a name="az-arcdata-dc-config-add"></a>az arcdata dc config add
構成ファイル内の json パスの位置に値を追加します。 以下の例はすべて Bash で指定されています。  別のコマンド ラインを使用している場合は、引用符の適切なエスケープが必要になることがあります。  あるいは、修正プログラム ファイルの機能を使用することもできます。
```bash
az arcdata dc config add 
```
### <a name="examples"></a>例
データ コントローラー ストレージを追加します。
```bash
az arcdata dc config add --path custom/control.json --json-values "spec.storage={"accessMode":"ReadWriteOnce","className":"managed-premium","size":"10Gi"}"
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
## <a name="az-arcdata-dc-config-remove"></a>az arcdata dc config remove
構成ファイル内の json パスの位置にある値を削除します。  以下の例はすべて Bash で指定されています。  別のコマンド ラインを使用している場合は、引用符の適切なエスケープが必要になることがあります。  あるいは、修正プログラム ファイルの機能を使用することもできます。
```bash
az arcdata dc config remove 
```
### <a name="examples"></a>例
例 1 - データ コントローラー ストレージを削除します。
```bash
az arcdata dc config remove --path custom/control.json --json-path ".spec.storage"
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
## <a name="az-arcdata-dc-config-replace"></a>az arcdata dc config replace
構成ファイル内の json パスの位置にある値を置き換えます。  以下の例はすべて Bash で指定されています。  別のコマンド ラインを使用している場合は、引用符の適切なエスケープが必要になることがあります。  あるいは、修正プログラム ファイルの機能を使用することもできます。
```bash
az arcdata dc config replace 
```
### <a name="examples"></a>例
例 1 - 1 つのエンドポイント (データ コントローラー エンドポイント) のポートを置き換えます。
```bash
az arcdata dc config replace --path custom/control.json --json-values "$.spec.endpoints[?(@.name=="Controller")].port=30080"
```
例 2 - データ コントローラー ストレージを置き換えます。
```bash
az arcdata dc config replace --path custom/control.json --json-values "spec.storage={"accessMode":"ReadWriteOnce","className":"managed-premium","size":"10Gi"}"
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
## <a name="az-arcdata-dc-config-patch"></a>az arcdata dc config patch
指定されたパッチ ファイルに従って、構成ファイルにパッチを適用します。 パスを構成する方法の詳細については、 http://jsonpatch.com/ を参照してください。 置き換え操作では、jsonpath ライブラリ https://jsonpath.com/ に従って、そのパス内で条件文を使用できます。 すべての修正プログラム json ファイルは、対応する操作 (add、replace、remove)、パス、および値で構成される修正プログラムの配列を含む "patch" のキーで始まる必要があります。 "remove" 操作には値は必要なく、パスだけが必要です。 次の例を参照してください。
```bash
az arcdata dc config patch 
```
### <a name="examples"></a>例
例 1 - 修正プログラム ファイルを使用して、1 つのエンドポイント (データ コントローラー エンドポイント) を置き換えます。
```bash
az arcdata dc config patch --path custom/control.json --patch ./patch.json

    Patch File Example (patch.json):
        {"patch":[{"op":"replace","path":"$.spec.endpoints[?(@.name=="Controller")].port","value":30080}]}
```
例 2 - データ コントローラー ストレージをパッチ ファイルで置き換えます。
```bash
az arcdata dc config patch --path custom/control.json --patch ./patch.json

    Patch File Example (patch.json):
        {"patch":[{"op":"replace","path":".spec.storage","value":{"accessMode":"ReadWriteMany","className":"managed-premium","size":"10Gi"}}]}
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
