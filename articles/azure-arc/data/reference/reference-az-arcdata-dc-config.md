---
title: az arcdata dc config リファレンス
titleSuffix: Azure Arc-enabled data services
description: az arcdata dc config コマンドのリファレンス記事。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 4b40220b6675649808a63b5258859004796986ab
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128650308"
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
az arcdata dc config init [--path -p] 
                          [--source -s]  
                          
[--force -f]
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
### <a name="optional-parameters"></a>省略可能なパラメーター
#### `--path -p`
構成プロファイルが配置される場所のファイル パス。既定値は \<cwd\>/custom です。
#### `--source -s`
構成プロファイルのソース: ["azure-arc-gke"、"azure-arc-eks"、"azure-arc-kubeadm"、"azure-arc-aks-default-storage"、"azure-arc-azure-openshift"、"azure-arc-ake"、"azure-arc-openshift"、"azure-arc-aks-dev-test"、"azure-arc-aks-hci"、"azure-arc-kubeadm-dev-test"、"azure-arc-aks-premium-storage"]
#### `--force -f`
ターゲット ファイルを強制的に上書きします。
### <a name="global-arguments"></a>グローバル引数
#### `--debug`
すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。
#### `--help -h`
このヘルプ メッセージを表示して終了します。
#### `--output -o`
出力形式。  json、jsonc、none、table、tsv、yaml、yamlc のいずれかの値を指定できます。  既定値: json。
#### `--query -q`
JMESPath クエリ文字列。 詳細と例については、[http://jmespath.org](http://jmespath.org) をご覧ください。
#### `--subscription`
サブスクリプションの名前または ID。 `az account set -s NAME_OR_ID` を使用して、既定のサブスクリプションを構成できます。
#### `--verbose`
ログの詳細レベルを上げます。 完全なデバッグ ログを表示するには `--debug` を使用します。
## <a name="az-arcdata-dc-config-list"></a>az arcdata dc config list
`arcdata dc config init` で使用するために、使用可能な構成プロファイルの選択肢の一覧を取得します
```bash
az arcdata dc config list [--config-profile -c] 
                          
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
### <a name="optional-parameters"></a>省略可能なパラメーター
#### `--config-profile -c`
既定の構成プロファイル: ["azure-arc-gke"、"azure-arc-eks"、"azure-arc-kubeadm"、"azure-arc-aks-default-storage"、"azure-arc-azure-openshift"、"azure-arc-ake"、"azure-arc-openshift"、"azure-arc-aks-dev-test"、"azure-arc-aks-hci"、"azure-arc-kubeadm-dev-test"、"azure-arc-aks-premium-storage"]
### <a name="global-arguments"></a>グローバル引数
#### `--debug`
すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。
#### `--help -h`
このヘルプ メッセージを表示して終了します。
#### `--output -o`
出力形式。  json、jsonc、none、table、tsv、yaml、yamlc のいずれかの値を指定できます。  既定値: json。
#### `--query -q`
JMESPath クエリ文字列。 詳細と例については、[http://jmespath.org](http://jmespath.org) をご覧ください。
#### `--subscription`
サブスクリプションの名前または ID。 `az account set -s NAME_OR_ID` を使用して、既定のサブスクリプションを構成できます。
#### `--verbose`
ログの詳細レベルを上げます。 完全なデバッグ ログを表示するには `--debug` を使用します。
## <a name="az-arcdata-dc-config-add"></a>az arcdata dc config add
構成ファイル内の json パスの位置に値を追加します。 以下の例はすべて Bash で指定されています。  別のコマンド ラインを使用している場合は、引用符の適切なエスケープが必要になることがあります。  あるいは、修正プログラム ファイルの機能を使用することもできます。
```bash
az arcdata dc config add --path -p 
                         --json-values -j
```
### <a name="examples"></a>例
データ コントローラー ストレージを追加します。
```bash
az arcdata dc config add --path custom/control.json --json-values "spec.storage={"accessMode":"ReadWriteOnce","className":"managed-premium","size":"10Gi"}"
```
### <a name="required-parameters"></a>必須のパラメーター
#### `--path -p`
設定する構成のデータ コントローラー構成ファイルのパス (例: `custom/control.json`)。
#### `--json-values -j`
値への json パスのキー値ペア リスト: key1.subkey1=value1,key2.subkey2=value2。 次のようなインライン json 値を指定できます。key='{"kind":"cluster","name":"test-cluster"}' or provide a file path, such as key=./values.json。 add コマンドでは、条件文はサポートされていません。  指定するインライン値が、"=" および "," を使用するキーと値のペア自体である場合は、それらの文字をエスケープします。  たとえば、key1="key2\=val2\,key3\=val3" のようになります。 パスの例については、[http://jsonpatch.com/](http://jsonpatch.com/) を参照してください。  配列にアクセスする場合は、インデックス (key.0=value など) を指定する必要があります。
### <a name="global-arguments"></a>グローバル引数
#### `--debug`
すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。
#### `--help -h`
このヘルプ メッセージを表示して終了します。
#### `--output -o`
出力形式。  json、jsonc、none、table、tsv、yaml、yamlc のいずれかの値を指定できます。  既定値: json。
#### `--query -q`
JMESPath クエリ文字列。 詳細と例については、[http://jmespath.org](http://jmespath.org) をご覧ください。
#### `--subscription`
サブスクリプションの名前または ID。 `az account set -s NAME_OR_ID` を使用して、既定のサブスクリプションを構成できます。
#### `--verbose`
ログの詳細レベルを上げます。 完全なデバッグ ログを表示するには `--debug` を使用します。
## <a name="az-arcdata-dc-config-remove"></a>az arcdata dc config remove
構成ファイル内の json パスの位置にある値を削除します。  以下の例はすべて Bash で指定されています。  別のコマンド ラインを使用している場合は、引用符の適切なエスケープが必要になることがあります。  あるいは、修正プログラム ファイルの機能を使用することもできます。
```bash
az arcdata dc config remove --path -p 
                            --json-path -j
```
### <a name="examples"></a>例
例 1 - データ コントローラー ストレージを削除します。
```bash
az arcdata dc config remove --path custom/control.json --json-path ".spec.storage"
```
### <a name="required-parameters"></a>必須のパラメーター
#### `--path -p`
設定する構成のデータ コントローラー構成ファイルのパス (例: `custom/control.json`)
#### `--json-path -j`
どの値を削除するかを示す jsonpatch ライブラリに基づいた json パスのリスト (`key1.subkey1,key2.subkey2` など)。 remove コマンドでは、条件文はサポートされていません。 パスの例については、[http://jsonpatch.com/](http://jsonpatch.com/) を参照してください。  配列にアクセスする場合は、インデックス (key.0=value など) を指定する必要があります。
### <a name="global-arguments"></a>グローバル引数
#### `--debug`
すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。
#### `--help -h`
このヘルプ メッセージを表示して終了します。
#### `--output -o`
出力形式。  json、jsonc、none、table、tsv、yaml、yamlc のいずれかの値を指定できます。  既定値: json。
#### `--query -q`
JMESPath クエリ文字列。 詳細と例については、[http://jmespath.org](http://jmespath.org) をご覧ください。
#### `--subscription`
サブスクリプションの名前または ID。 `az account set -s NAME_OR_ID` を使用して、既定のサブスクリプションを構成できます。
#### `--verbose`
ログの詳細レベルを上げます。 完全なデバッグ ログを表示するには `--debug` を使用します。
## <a name="az-arcdata-dc-config-replace"></a>az arcdata dc config replace
構成ファイル内の json パスの位置にある値を置き換えます。  以下の例はすべて Bash で指定されています。  別のコマンド ラインを使用している場合は、引用符の適切なエスケープが必要になることがあります。  あるいは、修正プログラム ファイルの機能を使用することもできます。
```bash
az arcdata dc config replace --path -p 
                             --json-values -j
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
### <a name="required-parameters"></a>必須のパラメーター
#### `--path -p`
設定する構成のデータ コントローラー構成ファイルのパス (例: `custom/control.json`)。
#### `--json-values -j`
値への json パスのキー値ペア リスト: key1.subkey1=value1,key2.subkey2=value2。 次のようなインライン json 値を指定できます。key='{"kind":"cluster","name":"test-cluster"}' or provide a file path, such as key=./values.json。 replace コマンドでは、jsonpath ライブラリによる条件文がサポートされています。  これを使用するには、パスを $ で始めます。 これにより、条件文 (-j $.key1.key2[?(@.key3=="someValue"].key4=value など) を実行できます。 指定するインライン値が、"=" および "," を使用するキーと値のペア自体である場合は、それらの文字をエスケープします。  たとえば、「 `key1="key2\=val2\,key3\=val3"` 」のように入力します。 以下の例を参照できます。 追加のヘルプについては、 https://jsonpath.com/ を参照してください
### <a name="global-arguments"></a>グローバル引数
#### `--debug`
すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。
#### `--help -h`
このヘルプ メッセージを表示して終了します。
#### `--output -o`
出力形式。  json、jsonc、none、table、tsv、yaml、yamlc のいずれかの値を指定できます。  既定値: json。
#### `--query -q`
JMESPath クエリ文字列。 詳細と例については、[http://jmespath.org](http://jmespath.org) をご覧ください。
#### `--subscription`
サブスクリプションの名前または ID。 `az account set -s NAME_OR_ID` を使用して、既定のサブスクリプションを構成できます。
#### `--verbose`
ログの詳細レベルを上げます。 完全なデバッグ ログを表示するには `--debug` を使用します。
## <a name="az-arcdata-dc-config-patch"></a>az arcdata dc config patch
指定されたパッチ ファイルに従って、構成ファイルにパッチを適用します。 パスを構成する方法の詳細については、 http://jsonpatch.com/ を参照してください。 置き換え操作では、jsonpath ライブラリ https://jsonpath.com/ に従って、そのパス内で条件文を使用できます。 すべての修正プログラム json ファイルは、対応する操作 (add、replace、remove)、パス、および値で構成される修正プログラムの配列を含む "patch" のキーで始まる必要があります。 "remove" 操作には値は必要なく、パスだけが必要です。 次の例を参照してください。
```bash
az arcdata dc config patch --path 
                           --patch-file -p
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
### <a name="required-parameters"></a>必須のパラメーター
#### `--path`
設定する構成のデータ コントローラー構成ファイルのパス (例: `custom/control.json`)。
#### `--patch-file -p`
jsonpatch ライブラリに基づいた修正プログラム json ファイルへのパス (http://jsonpatch.com/ )。 修正プログラム json ファイルは、実行する修正プログラム適用操作の配列である値を持つ "patch" という名前のキーで始める必要があります。 修正プログラム適用操作のパスの場合は、ドット表記 (ほとんどの操作での key1.key2 など) を使用できます。 置き換え操作を実行するときに、条件文を必要とする配列内の値を置き換えている場合は、パスを $ で始めて jsonpath 表記を使用してください。 これにより、条件文 ($.key1.key2[?(@.key3=="someValue"].key4 など) を実行できます。 次の例を参照してください。 条件文に関する追加のヘルプについては、 https://jsonpath.com/ を参照してください。
### <a name="global-arguments"></a>グローバル引数
#### `--debug`
すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。
#### `--help -h`
このヘルプ メッセージを表示して終了します。
#### `--output -o`
出力形式。  json、jsonc、none、table、tsv、yaml、yamlc のいずれかの値を指定できます。  既定値: json。
#### `--query -q`
JMESPath クエリ文字列。 詳細と例については、[http://jmespath.org](http://jmespath.org) をご覧ください。
#### `--subscription`
サブスクリプションの名前または ID。 `az account set -s NAME_OR_ID` を使用して、既定のサブスクリプションを構成できます。
#### `--verbose`
ログの詳細レベルを上げます。 完全なデバッグ ログを表示するには `--debug` を使用します。
