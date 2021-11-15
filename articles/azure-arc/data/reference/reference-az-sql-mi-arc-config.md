---
title: az sql mi-arc config リファレンス
titleSuffix: Azure Arc-enabled data services
description: az sql mi-arc config コマンドのリファレンス記事です。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 2dbe786f8a33528d028b4dc5f5476fa6f38a62b4
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845683"
---
# <a name="az-sql-mi-arc-config"></a>az sql mi-arc config
## <a name="commands"></a>コマンド
| コマンド | 説明|
| --- | --- |
[az sql mi-arc config init](#az-sql-mi-arc-config-init) | SQL マネージド インスタンスの CRD および仕様ファイルを初期化します。
[az sql mi-arc config add](#az-sql-mi-arc-config-add) | 構成ファイル内の json パスの値を追加します。
[az sql mi-arc config remove](#az-sql-mi-arc-config-remove) | 構成ファイル内の json パスの値を削除します。
[az sql mi-arc config replace](#az-sql-mi-arc-config-replace) | 構成ファイル内の json パスの値を置き換えます。
[az sql mi-arc config patch](#az-sql-mi-arc-config-patch) | json パッチ ファイルに基づいて、構成ファイルにパッチを適用します。
## <a name="az-sql-mi-arc-config-init"></a>az sql mi-arc config init
SQL マネージド インスタンスの CRD および仕様ファイルを初期化します。
```bash
az sql mi-arc config init 
```
### <a name="examples"></a>例
SQL マネージド インスタンスの CRD および仕様ファイルを初期化します。
```bash
az sql mi-arc config init --path ./template
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
## <a name="az-sql-mi-arc-config-add"></a>az sql mi-arc config add
構成ファイル内の json パスの位置に値を追加します。  以下の例はすべて Bash で指定されています。  別のコマンド ラインを使用している場合は、引用符の適切なエスケープが必要になることがあります。  あるいは、修正プログラム ファイルの機能を使用することもできます。
```bash
az sql mi-arc config add 
```
### <a name="examples"></a>例
例 1 - ストレージを追加します。
```bash
az sql mi-arc config add --path custom/spec.json --json-values "spec.storage={"accessMode":"ReadWriteOnce","className":"managed-premium","size":"10Gi"}"
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
## <a name="az-sql-mi-arc-config-remove"></a>az sql mi-arc config remove
構成ファイル内の json パスの位置にある値を削除します。 以下の例はすべて Bash で指定されています。 別のコマンド ラインを使用している場合は、引用符の適切なエスケープが必要になることがあります。 あるいは、修正プログラム ファイルの機能を使用することもできます。
```bash
az sql mi-arc config remove 
```
### <a name="examples"></a>例
例 1 - ストレージを削除します。
```bash
az sql mi-arc config remove --path custom/spec.json --json-path ".spec.storage"
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
## <a name="az-sql-mi-arc-config-replace"></a>az sql mi-arc config replace
構成ファイル内の json パスの位置にある値を置き換えます。  以下の例はすべて Bash で指定されています。  別のコマンド ラインを使用している場合は、引用符の適切なエスケープが必要になることがあります。  あるいは、修正プログラム ファイルの機能を使用することもできます。
```bash
az sql mi-arc config replace 
```
### <a name="examples"></a>例
例 1 - 1 つのエンドポイントのポートを置き換えます。
```bash
az sql mi-arc config replace --path custom/spec.json --json-values "$.spec.endpoints[?(@.name=="Controller")].port=30080"
```
例 2 - ストレージを置き換えます。
```bash
az sql mi-arc config replace --path custom/spec.json --json-values "spec.storage={"accessMode":"ReadWriteOnce","className":"managed-premium","size":"10Gi"}"
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
## <a name="az-sql-mi-arc-config-patch"></a>az sql mi-arc config patch
指定されたパッチ ファイルに従って、構成ファイルにパッチを適用します。 パスを構成する方法の詳細については、 http://jsonpatch.com/ を参照してください。 置き換え操作では、jsonpath ライブラリ https://jsonpath.com/ に従って、そのパス内で条件文を使用できます。 すべての修正プログラム json ファイルは、対応する操作 (add、replace、remove)、パス、および値で構成される修正プログラムの配列を含む `patch` のキーで始まる必要があります。 `remove` 操作には値は必要なく、パスだけが必要です。 次の例を参照してください。
```bash
az sql mi-arc config patch 
```
### <a name="examples"></a>例
例 1 - 修正プログラム ファイルを使用して、1 つのエンドポイントのポートを置き換えます。
```bash
az sql mi-arc config patch --path custom/spec.json --patch ./patch.json

    Patch File Example (patch.json):
        {"patch":[{"op":"replace","path":"$.spec.endpoints[?(@.name=="Controller")].port","value":30080}]}
```
例 2 - 修正プログラム ファイルを使用して、ストレージを置き換えます。
```bash
az sql mi-arc config patch --path custom/spec.json --patch ./patch.json

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
