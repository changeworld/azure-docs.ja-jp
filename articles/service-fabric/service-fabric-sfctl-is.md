---
title: Azure Service Fabric CLI- sfctl is | Microsoft Docs
description: "Service Fabric CLI sfctl is のコマンドについて説明します。"
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 02/22/2018
ms.author: ryanwi
ms.openlocfilehash: d3a4c7a308bdf7132cebffc13a5e7214ec73eaf2
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2018
---
# <a name="sfctl-is"></a>sfctl is
クエリを実行し、インフラストラクチャ サービスにコマンドを送信します。

## <a name="commands"></a>コマンド

|コマンド|[説明]|
| --- | --- |
|    コマンド| 特定の Infrastructure Service インスタンスで管理コマンドを呼び出します。|
|    クエリ  | 特定の Infrastructure Service インスタンスで読み取り専用クエリを呼び出します。|


## <a name="sfctl-is-command"></a>sfctl is command
特定の Infrastructure Service インスタンスで管理コマンドを呼び出します。

構成済みの Infrastructure Service のインスタンスが 1 つ以上あるクラスターの場合、この API を使用して、Infrastructure Service の特定のインスタンスにインフラストラクチャ固有のコマンドを送信することができます。 使用可能なコマンドと、対応する応答の形式は、クラスターが実行されているインフラストラクチャによって異なります。 この API は、Service Fabric プラットフォームをサポートしています。コードから直接使用するものではありません。 

### <a name="arguments"></a>引数

|引数|[説明]|
| --- | --- |
| --command [必須]| 呼び出されるコマンドのテキスト。 コマンドのコンテンツは、インフラストラクチャに固有です。 |
| --service-id     | Infrastructure Service の ID。 ID は "fabric:" URI スキームのない Infrastructure Service の完全な名前です。 このパラメーターは、実行している Infrastructure Service のインスタンスを複数持つクラスターにのみ必要です。|
| --timeout -t     | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|[説明]|
| --- | --- |
| --debug          | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h        | このヘルプ メッセージを表示して終了します。|
| --output -o      | 出力形式。  使用できる値は、json、jsonc、table、tsv です。  既定値は json です。|
| --query          | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose        | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。|

## <a name="sfctl-is-query"></a>sfctl is query
特定の Infrastructure Service インスタンスで読み取り専用クエリを呼び出します。

構成済みの Infrastructure Service のインスタンスが 1 つ以上あるクラスターの場合、この API を使用して、Infrastructure Service の特定のインスタンスにインフラストラクチャ固有のクエリを送信することができます。 使用可能なコマンドと、対応する応答の形式は、クラスターが実行されているインフラストラクチャによって異なります。 この API は、Service Fabric プラットフォームをサポートしています。コードから直接使用するものではありません。

### <a name="arguments"></a>引数

|引数|[説明]|
| --- | --- |
| --command [必須]| 呼び出されるコマンドのテキスト。 コマンドのコンテンツは、インフラストラクチャに固有です。|
| --service-id     | Infrastructure Service の ID。 ID は "fabric:" URI スキームのない Infrastructure Service の完全な名前です。 このパラメーターは、実行している Infrastructure Service のインスタンスを複数持つクラスターにのみ必要です。|
| --timeout -t     | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|[説明]|
| --- | --- |
| --debug          | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h        | このヘルプ メッセージを表示して終了します。|
| --output -o      | 出力形式。  使用できる値は、json、jsonc、table、tsv です。  既定値は json です。|
| --query          | JMESPath クエリ文字列。 詳細については、http://jmespath.org/ を参照してください。|
| --verbose        | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。|

## <a name="next-steps"></a>次の手順
- Service Fabric CLI を[セットアップ](service-fabric-cli.md)します。
- [サンプル スクリプト](/azure/service-fabric/scripts/sfctl-upgrade-application)を使用して、Service Fabric CLI の使用方法を学習します。