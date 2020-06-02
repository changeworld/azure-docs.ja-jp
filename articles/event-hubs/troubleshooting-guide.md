---
title: トラブルシューティング ガイド - Azure Event Hubs | Microsoft Docs
description: この記事では、Azure Event Hubs メッセージングの例外と推奨アクションの一覧を示します。
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 01/16/2020
ms.author: shvija
ms.openlocfilehash: ab3cbdf938409f4eacffa10ae5cb20f8c36b5856
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124671"
---
# <a name="azure-event-hubs---troubleshooting-guide"></a>Azure Event Hubs - トラブルシューティング ガイド
この記事では、Azure Event Hubs の使用時に発生する可能性のあるいくつかの問題のトラブルシューティングに関するヒントと推奨事項について説明します。

## <a name="connectivity-certificate-or-timeout-issues"></a>接続、証明書、またはタイムアウトの問題
次の手順は、*.servicebus.windows.net のすべてのサービスの接続/証明書/タイムアウトの問題のトラブルシューティングに役立ちます。 

- `https://<yournamespacename>.servicebus.windows.net/` の参照または [wget](https://www.gnu.org/software/wget/) を行います。 これは、IP フィルタリング、仮想ネットワーク、または証明書チェーンの問題 (java SDK の使用時に最も一般的) があるかどうかを確認するのに役立ちます。

    成功したメッセージの例を次に示します。
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    失敗したエラー メッセージの例を次に示します。

    ```json
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- 次のコマンドを実行して、ファイアウォールでポートがブロックされているかどうかを確認します。 使用されるポートは、443 (HTTPS)、5671 (AMQP)、9093 (Kafka) です。 使用するライブラリによっては、他のポートも使用されます。 5671 ポートがブロックされているかどうかを確認するサンプル コマンドを次に示します。

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    Linux の場合:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- 接続の問題が断続的に発生する場合は、次のコマンドを実行して、破棄されたパケットがあるかどうかを確認します。 このコマンドは、1 秒ごとに 25 件の異なる TCP 接続をサービスと確立しようとします。 次に、成功または失敗した数を確認し、TCP 接続の待機時間も確認します。 `psping` ツールは、[こちら](/sysinternals/downloads/psping)からダウンロードできます。

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespacename>.servicebus.windows.net:5671 -nobanner     
    ```
    `tnc` や`ping` などの他のツールを使用している場合は、同等のコマンドを使用できます。 
- 前の手順で解決できない場合は、ネットワーク トレースを取得して [Wireshark](https://www.wireshark.org/) などのツールを使用して分析します。 必要に応じて [Microsoft サポート](https://support.microsoft.com/) にお問い合わせください。 

## <a name="issues-that-may-occur-with-service-upgradesrestarts"></a>サービスのアップグレードまたは再起動で発生する可能性のある問題
バックエンド サービスのアップグレードと再起動によって、アプリケーションに次のような影響が生じる可能性があります。

- 要求が一時的に調整される場合があります。
- 受信メッセージや要求がドロップされる可能性があります。
- ログ ファイルにエラー メッセージが含まれる可能性があります。
- アプリケーションが数秒間サービスから切断される可能性があります。

アプリケーションのコードで SDK を利用している場合、再試行ポリシーは既に組み込まれており、アクティブになっています。 アプリケーションやワークフローに大きな影響を与えずに、アプリケーションは再接続されます。


## <a name="next-steps"></a>次のステップ

Event Hubs の詳細については、次のリンク先を参照してください:

* [Event Hubs の概要](event-hubs-what-is-event-hubs.md)
* [Event Hub を作成する](event-hubs-create.md)
* [Event Hubs の FAQ](event-hubs-faq.md)
