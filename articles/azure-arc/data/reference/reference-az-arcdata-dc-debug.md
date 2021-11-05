---
title: az arcdata dc debug リファレンス
titleSuffix: Azure Arc-enabled data services
description: az arcdata dc debug コマンドのリファレンス記事。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: e230d788a06865a8868f90fb1195c9fb701c2750
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131847795"
---
# <a name="az-arcdata-dc-debug"></a>az arcdata dc debug
## <a name="commands"></a>コマンド
| コマンド | 説明|
| --- | --- |
[az arcdata dc debug copy-logs](#az-arcdata-dc-debug-copy-logs) | ログをコピーします。
[az arcdata dc debug dump](#az-arcdata-dc-debug-dump) | メモリ ダンプをトリガーします。
## <a name="az-arcdata-dc-debug-copy-logs"></a>az arcdata dc debug copy-logs
データ コントローラーからデバッグ ログをコピーします。お使いのシステムには、Kubernetes を構成する必要があります。
```bash
az arcdata dc debug copy-logs 
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
## <a name="az-arcdata-dc-debug-dump"></a>az arcdata dc debug dump
メモリ ダンプをトリガーし、それをコンテナーからコピーします。システム上に Kubernetes の構成が必要です。
```bash
az arcdata dc debug dump 
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
