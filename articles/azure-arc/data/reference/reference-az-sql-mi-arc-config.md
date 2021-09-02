---
title: az sql mi-arc config リファレンス
titleSuffix: Azure Arc-enabled data services
description: az sql mi-arc config コマンドのリファレンス記事です。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: a8fad1be3245792c6967f56c9026bf8efee4d6e9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779435"
---
# <a name="az-sql-mi-arc-config"></a>az sql mi-arc config
## <a name="commands"></a>コマンド
| command | 説明|
| --- | --- |
[az sql mi-arc config init](#az-sql-mi-arc-config-init) | SQL マネージド インスタンスの CRD および仕様ファイルを初期化します。
[az sql mi-arc config add](#az-sql-mi-arc-config-add) | 構成ファイル内の json パスの値を追加します。
[az sql mi-arc config remove](#az-sql-mi-arc-config-remove) | 構成ファイル内の json パスの値を削除します。
[az sql mi-arc config replace](#az-sql-mi-arc-config-replace) | 構成ファイル内の json パスの値を置き換えます。
[az sql mi-arc config patch](#az-sql-mi-arc-config-patch) | json パッチ ファイルに基づいて、構成ファイルにパッチを適用します。
## <a name="az-sql-mi-arc-config-init"></a>az sql mi-arc config init
SQL マネージド インスタンスの CRD および仕様ファイルを初期化します。
```bash
az sql mi-arc config init --path -p 
                          
```
### <a name="examples"></a>例
SQL マネージド インスタンスの CRD および仕様ファイルを初期化します。
```bash
az sql mi-arc config init --path ./template
```
### <a name="required-parameters"></a>必須のパラメーター
#### `--path -p`
SQL マネージド インスタンスの CRD および仕様が書き込まれるパス。
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
## <a name="az-sql-mi-arc-config-add"></a>az sql mi-arc config add
構成ファイル内の json パスの位置に値を追加します。  以下の例はすべて Bash で指定されています。  別のコマンド ラインを使用している場合は、引用符の適切なエスケープが必要になることがあります。  あるいは、修正プログラム ファイルの機能を使用することもできます。
```bash
az sql mi-arc config add --path -p 
                         --json-values -j
```
### <a name="examples"></a>例
例 1 - ストレージを追加します。
```bash
az sql mi-arc config add --path custom/spec.json --json-values "spec.storage={"accessMode":"ReadWriteOnce","className":"managed-premium","size":"10Gi"}"
```
### <a name="required-parameters"></a>必須のパラメーター
#### `--path -p`
カスタム リソース仕様へのパス (つまり、custom/spec.json)
#### `--json-values -j`
値への json パスのキー値ペア リスト: key1.subkey1=value1,key2.subkey2=value2。 次のようなインライン json 値を指定できます。key='{"kind":"cluster","name":"test-cluster"}' or provide a file path, such as key=./values.json。 add コマンドでは、条件文はサポートされていません。  指定するインライン値が、"=" および "," を使用するキーと値のペア自体である場合は、それらの文字をエスケープしてください。  たとえば、key1="key2\=val2\,key3\=val3" のようになります。 パスの例については、 http://jsonpatch.com/ を参照してください。  配列にアクセスする場合は、インデックス (key.0=value など) を指定する必要があります。
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
## <a name="az-sql-mi-arc-config-remove"></a>az sql mi-arc config remove
構成ファイル内の json パスの位置にある値を削除します。  以下の例はすべて Bash で指定されています。  別のコマンド ラインを使用している場合は、引用符の適切なエスケープが必要になることがあります。  あるいは、修正プログラム ファイルの機能を使用することもできます。
```bash
az sql mi-arc config remove --path -p 
                            --json-path -j
```
### <a name="examples"></a>例
例 1 - ストレージを削除します。
```bash
az sql mi-arc config remove --path custom/spec.json --json-path ".spec.storage"
```
### <a name="required-parameters"></a>必須のパラメーター
#### `--path -p`
カスタム リソース仕様へのパス (つまり、custom/spec.json)
#### `--json-path -j`
どの値を削除するかを示す jsonpatch ライブラリに基づいた json パスのリスト (key1.subkey1,key2.subkey2 など)。 remove コマンドでは、条件文はサポートされていません。 パスの例については、 http://jsonpatch.com/ を参照してください。  配列にアクセスする場合は、インデックス (key.0=value など) を指定する必要があります。
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
## <a name="az-sql-mi-arc-config-replace"></a>az sql mi-arc config replace
構成ファイル内の json パスの位置にある値を置き換えます。  以下の例はすべて Bash で指定されています。  別のコマンド ラインを使用している場合は、引用符の適切なエスケープが必要になることがあります。  あるいは、修正プログラム ファイルの機能を使用することもできます。
```bash
az sql mi-arc config replace --path -p 
                             --json-values -j
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
### <a name="required-parameters"></a>必須のパラメーター
#### `--path -p`
カスタム リソース仕様へのパス (つまり、custom/spec.json)
#### `--json-values -j`
値への json パスのキー値ペア リスト: key1.subkey1=value1,key2.subkey2=value2。 次のようなインライン json 値を指定できます。key='{"kind":"cluster","name":"test-cluster"}' or provide a file path, such as key=./values.json。 replace コマンドでは、jsonpath ライブラリによる条件文がサポートされています。  これを使用するには、パスを $ で始めます。 これにより、条件文 (-j $.key1.key2[?(@.key3=="someValue"].key4=value など) を実行できます。 指定するインライン値が、"=" および "," を使用するキーと値のペア自体である場合は、それらの文字をエスケープしてください。  たとえば、key1="key2\=val2\,key3\=val3" のようになります。 以下の例を参照できます。 追加のヘルプについては、 https://jsonpath.com/ を参照してください。
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
## <a name="az-sql-mi-arc-config-patch"></a>az sql mi-arc config patch
指定されたパッチ ファイルに従って、構成ファイルにパッチを適用します。 パスを構成する方法の詳細については、 http://jsonpatch.com/ を参照してください。 置き換え操作では、jsonpath ライブラリ https://jsonpath.com/ に従って、そのパス内で条件文を使用できます。 すべての修正プログラム json ファイルは、対応する操作 (add、replace、remove)、パス、および値で構成される修正プログラムの配列を含む "patch" のキーで始まる必要があります。 "remove" 操作には値は必要なく、パスだけが必要です。 次の例を参照してください。
```bash
az sql mi-arc config patch --path -p 
                           --patch-file
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
### <a name="required-parameters"></a>必須のパラメーター
#### `--path -p`
カスタム リソース仕様へのパス (つまり、custom/spec.json)
#### `--patch-file`
jsonpatch ライブラリに基づいた修正プログラム json ファイルへのパス (http://jsonpatch.com/ )。 修正プログラム json ファイルは、実行する修正プログラム適用操作の配列である値を持つ "patch" という名前のキーで始める必要があります。 修正プログラム適用操作のパスの場合は、ドット表記 (ほとんどの操作での key1.key2 など) を使用できます。 置き換え操作を実行するときに、条件文を必要とする配列内の値を置き換えている場合は、パスを $ で始めて jsonpath 表記を使用してください。 これにより、条件文 ($.key1.key2[?(@.key3=="someValue"].key4 など) を実行できます。 以下の例を参照してください。 条件文に関する追加のヘルプについては、 https://jsonpath.com/ を参照してください。
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
