---
title: az arcdata resource-kind リファレンス
titleSuffix: Azure Arc-enabled data services
description: az arcdata resource-kind コマンドの参照記事。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 597bc660140ec2f0ac856a3f6c67db600480aa63
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780877"
---
# <a name="az-arcdata-resource-kind"></a>az arcdata resource-kind
## <a name="commands"></a>コマンド
| command | 説明|
| --- | --- |
[az arcdata resource-kind list](#az-arcdata-resource-kind-list) | 定義および作成できる Arc で使用できるカスタム リソースの種類を一覧表示します。
[az arcdata resource-kind get](#az-arcdata-resource-kind-get) | Arc resource-kind のテンプレート ファイルを取得します。
## <a name="az-arcdata-resource-kind-list"></a>az arcdata resource-kind list
定義および作成できる Arc で使用できるカスタム リソースの種類を一覧表示します。 一覧表示したら、そのカスタム リソースの定義または作成に必要なテンプレート ファイルの取得に進みます。
```bash
az arcdata resource-kind list 
```
### <a name="examples"></a>使用例
Arc で使用できるカスタム リソースの種類を一覧表示するコマンド例です。
```bash
az arcdata resource-kind list
```
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
## <a name="az-arcdata-resource-kind-get"></a>az arcdata resource-kind get
Arc resource-kind のテンプレート ファイルを取得します。
```bash
az arcdata resource-kind get --kind -k 
                             [--dest -d]
```
### <a name="examples"></a>使用例
Arc resource-kind の CRD テンプレート ファイルを取得するコマンド例です。
```bash
az arcdata resource-kind get --kind SqlManagedInstance
```
### <a name="required-parameters"></a>必須のパラメーター
#### `--kind -k`
テンプレート ファイルを使用する arc のリソースの種類。
### <a name="optional-parameters"></a>省略可能のパラメーター
#### `--dest -d`
テンプレート ファイルを配置するディレクトリ。
`template`
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
