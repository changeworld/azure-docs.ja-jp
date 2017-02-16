---
title: "C および Apache Storm での Event Hubs の使用 | Microsoft Docs"
description: "このチュートリアルでは、C でイベントを送信し、Apache Storm クラスターでそれを受信するための Azure Event Hubs の使用方法について説明します。"
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 3b15825e-c53c-471b-870c-686bff46885a
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: c
ms.devlang: java
ms.topic: article
ms.date: 11/30/2016
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: 33a960ae39ab8fa89126201fd67f13e5536413ef
ms.openlocfilehash: 7bad4df26d10ba8edae311b41d0c07be2db374f4


---
# <a name="get-started-with-event-hubs"></a>Event Hubs の使用
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>はじめに
Event Hubs は、拡張性の高いインジェスト システムで、1 秒あたり何百万ものイベントを取り込むことができます。そのためアプリケーションは、接続されているデバイスやアプリケーションによって生成された大量のデータを処理し、分析できます。 Event Hubs に収集されたデータは、任意のリアルタイム分析プロバイダーやストレージ クラスターを使用して転送と格納できます。

詳細については、「 [Event Hubs の概要]」を参照してください。

このチュートリアルでは、C のコンソール アプリケーションを使用して、イベント ハブにメッセージをインジェストし、Apache Storm を使用して並列で取得する方法を学習します。

このチュートリアルを最後まで行うには、以下のものが必要です。

* C の開発環境。 このチュートリアルでは、Ubuntu 14.04 での [Azure Linux VM](../virtual-machines/virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 上の GCC スタックを想定しています。 他の環境用の手順は、外部リンクで提供されます。
* [Maven](http://maven.apache.org/)を実行するように構成された Java 開発環境。 このチュートリアルでは、 [Eclipse](https://www.eclipse.org/)を想定しています。
* アクティブな Azure アカウント。 アカウントがない場合は、無料アカウントを数分で作成することができます。 詳細については、 [Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/)を参照してください。

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[!INCLUDE [service-bus-event-hubs-get-started-send-c](../../includes/service-bus-event-hubs-get-started-send-c.md)]

[!INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## <a name="run-the-applications"></a>アプリケーションの実行
これで、アプリケーションを実行する準備が整いました。

1. Eclipse で **LogTopology** クラスを実行し、すべてのパーティションの受信側が起動するまで待機します。
2. **sender** プログラムを実行し、受信側ウィンドウに表示されるイベントを確認します。
   
   ![][23]

> [!NOTE]
> このチュートリアルでは、Storm をローカル モードで開発目的にのみ使用します。 Storm のデプロイとパターンの詳細については、「[HDInsight Storm の概要]」と [Apache Storm] の公式ドキュメントを参照してください。

## <a name="next-steps"></a>次のステップ
Event Hubs と Storm を統合するアプリケーションの開発には、次のリソースを使用できます。

* [Storm と HDInsight でのセンサー データの分析][Analyzing sensor data with Storm and HDInsight]: Hadoop クラスターのセンサー データを取り込むための Event Hubs、Storm、および HBase を使用した完全なシナリオ チュートリアルです。
* [Storm と HDInsight での SCP.NET および C# を使用したストリーミング データ処理アプリケーションの開発][Develop streaming data processing applications with SCP.NET and C# on Storm and HDInsight]: C# を使用して Storm のパイプラインを作成する方法に関するチュートリアルです。

<!-- Images. -->
[23]: ./media/event-hubs-c-storm-getstarted/receive-storm3.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs の概要]: event-hubs-overview.md

[Apache Storm]: https://storm.incubator.apache.org
[HDInsight Storm の概要]: ../hdinsight/hdinsight-storm-overview.md
[Analyzing sensor data with Storm and HDInsight]: ../hdinsight/hdinsight-storm-sensor-data-analysis.md
[Develop streaming data processing applications with SCP.NET and C# on Storm and HDInsight]: ../hdinsight/hdinsight-storm-develop-csharp-visual-studio-topology.md



<!--HONumber=Dec16_HO1-->


