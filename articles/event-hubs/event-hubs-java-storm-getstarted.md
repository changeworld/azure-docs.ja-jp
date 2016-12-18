---
title: "Java および Apache Storm での Event Hubs の使用 | Microsoft Docs"
description: "このチュートリアルでは、Java でイベントを送信し、Apache Storm クラスターでイベントを受信するための Azure Event Hubs の使用方法について説明します。"
services: event-hubs
documentationcenter: 
author: fsautomata
manager: timlt
editor: 
ms.assetid: 385869bd-1ebe-44ae-8113-cc4679a568eb
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: eacafbcddb81085c706d8c4b64c426b21ce06b79


---
# <a name="get-started-with-event-hubs"></a>Event Hubs の使用
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>はじめに
Event Hubs は、拡張性の高いインジェスト システムで、1 秒あたり何百万ものイベントを取り込むことができます。そのためアプリケーションは、接続されているデバイスやアプリケーションによって生成された大量のデータを処理し、分析できます。 Event Hubs に収集されたデータは、任意のリアルタイム分析プロバイダーやストレージ クラスターを使用して転送と格納できます。

詳細については、[Event Hubs の概要][Event Hubs の概要]に関するページを参照してください。

このチュートリアルでは、Java のコンソール アプリケーションを使用してイベント ハブにメッセージを収集し、Apache Storm を使用して並列で取得する方法を学習します。

このチュートリアルを最後まで行うには、以下のものが必要です。

* [Maven](http://maven.apache.org/)を実行するように構成された Java 開発環境。 このチュートリアルでは、 [Eclipse](https://www.eclipse.org/)を想定しています。
* アクティブな Azure アカウント。 アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。 詳細については、 [Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/)を参照してください。

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[!INCLUDE [service-bus-event-hubs-get-started-send-java](../../includes/service-bus-event-hubs-get-started-send-java.md)]

[!INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## <a name="run-the-applications"></a>アプリケーションの実行
これで、アプリケーションを実行する準備が整いました。

1. Eclipse で **LogTopology** クラスを実行し、すべてのパーティションの受信側が起動するまで待機します。
2. **Sender** プロジェクトを実行し、コンソール ウィンドウで **Enter** キーを押して、受信側ウィンドウに表示されるイベントを確認します。
   
    ![][22]

> [!NOTE]
> このチュートリアルでは、Storm をローカル モードで開発目的にのみ使用します。 Storm の開発とパターンの詳細については、[HDInsight Storm の概要][HDInsight Storm の概要]に関するページと [Apache Storm][Apache Storm] の公式ドキュメントを参照してください。
> 
> 

## <a name="next-steps"></a>次のステップ
Event Hubs と Storm を統合するアプリケーションの開発には、次のリソースを使用できます。

* [HDInsight (Hadoop) での Storm と HBase を使用したセンサー データの分析] 」は、Hadoop クラスター内のセンサー データを取り込むための Event Hubs、Storm、および HBase を使用した完全なシナリオ チュートリアルです。
* [HDInsight の Storm で SCP.NET と C# を使用したストリーミング データ処理アプリケーションの開発][HDInsight の Storm で SCP.NET と C# を使用したストリーミング データ処理アプリケーションの開発]に関するページは、C# を使用した Storm のパイプラインの作成に関するチュートリアルです。

<!-- Images. -->
[22]: ./media/event-hubs-java-storm-getstarted/receive-storm2.png

<!-- Links -->
[Azure クラシック ポータル]: https://manage.windowsazure.com/
[イベント プロセッサ ホスト]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs の概要]: event-hubs-overview.md

[Apache Storm]: https://storm.incubator.apache.org
[HDInsight Storm の概要]: ../hdinsight/hdinsight-storm-overview.md
[HDInsight (Hadoop) での Storm と HBase を使用したセンサー データの分析]: ../hdinsight/hdinsight-storm-sensor-data-analysis.md
[HDInsight の Storm で SCP.NET と C# を使用したストリーミング データ処理アプリケーションの開発]: ../hdinsight/hdinsight-storm-develop-csharp-visual-studio-topology.md



<!--HONumber=Nov16_HO3-->


