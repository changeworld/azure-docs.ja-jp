---
title: az arcdata resource-kind リファレンス
titleSuffix: Azure Arc-enabled data services
description: az arcdata resource-kind コマンドの参照記事。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: ad138bfc322aa109078e622b26d1f5254ec789ca
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852984"
---
# <a name="az-arcdata-resource-kind"></a>az arcdata resource-kind
## <a name="commands"></a>コマンド
| コマンド | 説明|
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
出力形式。  使用できる値: json、jsonc、table、tsv。  既定値: json。
#### `--query -q`
JMESPath クエリ文字列。 詳細と例については、[http://jmespath.org/](http://jmespath.org) を参照してください。
#### `--verbose`
ログの詳細レベルを上げます。 完全なデバッグ ログを表示するには `--debug` を使用します。
## <a name="az-arcdata-resource-kind-get"></a>az arcdata resource-kind get
Arc resource-kind のテンプレート ファイルを取得します。
```bash
az arcdata resource-kind get 
```
### <a name="examples"></a>使用例
Arc resource-kind の CRD テンプレート ファイルを取得するコマンド例です。
```bash
az arcdata resource-kind get --kind SqlManagedInstance
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
