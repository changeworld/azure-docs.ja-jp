---
title: Kafka 対応 Azure Event Hubs の作成 | Microsoft Docs
description: Azure portal を使用して Kafka 対応 Azure Event Hubs の名前空間を作成します
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.date: 05/07/2018
ms.author: shvija
ms.openlocfilehash: 7a05a89fdf99efb6470ee9c8695f349cf22b8ebb
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37442129"
---
# <a name="create-kafka-enabled-event-hubs"></a>Kafka 対応イベント ハブの作成

Azure Event Hubs はビッグ データ ストリーミング PaaS (サービスとしてのプラットフォーム) です。毎秒数百万のイベントが取り込まれ、リアルタイムの分析と視覚化のために低遅延かつ高いスループットが提供されます。

Kafka エコシステム用 Azure Event Hubs ではエンドポイントが提供されます。 このエンドポイントにより、Event Hubs 名前空間で [Apache Kafka](https://kafka.apache.org/intro) メッセージ プロトコルと API をネイティブに認識することができます。 この機能を利用すれば、プロトコル クライアントを変更したり、独自のクラスターを実行したりすることなく、Kafka トピックの場合と同様に、イベント ハブと通信することができます。 Kafka エコシステム用 Event Hubs では、[Apache Kafka バージョン 1.0](https://kafka.apache.org/10/documentation.html) 以降がサポートされています。

この記事では、Event Hubs 名前空間を作成し、Kafka 対応イベント ハブに Kafka アプリケーションを接続するために必要な接続文字列を取得する方法を説明します。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)を作成してください。

## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Kafka 対応 Event Hubs 名前空間の作成

1. [Azure portal][Azure portal] にサインインし、画面の左上にある **[リソースの作成]** をクリックします。

2. Event Hubs を検索し、以下に示すオプションを選択します。
    
    ![ポータルでの Event Hubs の検索](./media/event-hubs-create-kafka-enabled/event-hubs-create-event-hubs.png)
 
3. 一意の名前を指定し、名前空間で Kafka を有効にします。 **Create** をクリックしてください。
    
    ![名前空間の作成](./media/event-hubs-create-kafka-enabled/create-kafka-namespace.png)
 
4. 名前空間が作成されたら、**[設定]** タブで **[共有アクセス ポリシー]** をクリックして接続文字列を取得します。

    ![[共有アクセス ポリシー] をクリックする](./media/event-hubs-create/create-event-hub7.png)

5. 既定値の **RootManageSharedAccessKey** を選択することも、新しいポリシーを追加することもできます。 ポリシー名をクリックして、接続文字列をコピーします。 
    
    ![ポリシーの選択](./media/event-hubs-create/create-event-hub8.png)
 
6. この接続文字列を Kafka アプリケーション構成に追加します。

これで、Kafka プロトコルを使用するアプリケーションからイベントを Event Hubs にストリーミングできます。

## <a name="next-steps"></a>次の手順

Event Hubs の詳細については、次のリンク先を参照してください。

* [Kafka アプリケーションから Event Hubs へのストリーム](event-hubs-quickstart-kafka-enabled-event-hubs.md)
* [Kafka エコシステム用 Event Hubs について確認する](event-hubs-for-kafka-ecosystem-overview.md)
* [Event Hubs について確認する](event-hubs-what-is-event-hubs.md)


[Azure portal]: https://portal.azure.com/
