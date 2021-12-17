---
title: az sql mi-arc dag リファレンス
titleSuffix: Azure Arc-enabled data services
description: az sql mi-arc dag コマンドのリファレンス記事です。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: f568f7086f2730bde53a1cd4dd31cf25092c5dd7
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131848650"
---
# <a name="az-sql-mi-arc-dag"></a>az sql mi-arc dag
## <a name="commands"></a>コマンド
| コマンド | 説明|
| --- | --- |
[az sql mi-arc dag create](#az-sql-mi-arc-dag-create) | 分散型可用性グループのカスタム リソースを作成します
[az sql mi-arc dag delete](#az-sql-mi-arc-dag-delete) | sqlmi インスタンス上の分散可用性グループのカスタム リソースを削除します。
[az sql mi-arc dag show](#az-sql-mi-arc-dag-show) | 分散可用性グループのカスタム リソースを表示します。
## <a name="az-sql-mi-arc-dag-create"></a>az sql mi-arc dag create
分散型可用性グループを作成するための分散型可用性グループのカスタム リソースを作成します
```bash
az sql mi-arc dag create 
```
### <a name="examples"></a>例
例 1 - 分散可用性グループのカスタム リソース dagCr1 を作成して、ローカル sqlmi インスタンス sqlmi1 とリモート sqlmi インスタンス sqlmi2 の間に分散可用性グループ dagName1 を作成します。 それには、リモート sqlmi プライマリ ミラー remotePrimary:5022 とリモート sqlmi ミラー エンドポイント証明書ファイル ./sqlmi2.cer が必要です。
```bash
az sql mi-arc dag create --name dagCr1 --dag-name dagName1 --local-instance-name sqlmi1 --local-primary local --remote-instance-name sqlmi2 --remote-mirroring-url remotePrimary:5022 --remote-mirroring-cert-file ./sqlmi2.cer --use-k8s
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
## <a name="az-sql-mi-arc-dag-delete"></a>az sql mi-arc dag delete
sqlmi インスタンス上の分散可用性グループのカスタム リソースを削除して、分散可用性グループを削除します。 カスタム リソース名が必要です。
```bash
az sql mi-arc dag delete 
```
### <a name="examples"></a>例
例 1 - dagCr1 という名前の分散型可用性グループ リソースを削除します。
```bash
az sql mi-arc dag delete --name dagCr1 --use-k8s
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
## <a name="az-sql-mi-arc-dag-show"></a>az sql mi-arc dag show
分散可用性グループのカスタム リソースを表示します。 カスタム リソース名が必要です
```bash
az sql mi-arc dag show 
```
### <a name="examples"></a>例
例 1 - dagCr1 という名前の分散型可用性グループ リソースを表示します。
```bash
az sql mi-arc dag show --name dagCr1 --use-k8s
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
