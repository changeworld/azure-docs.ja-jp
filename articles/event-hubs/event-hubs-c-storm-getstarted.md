<properties
	pageTitle="C および Apache Storm での Event Hubs の使用 | Microsoft Azure"
	description="このチュートリアルでは、C でイベントを送信し、Apache Storm クラスターでそれを受信するための Azure Event Hubs の使用方法について説明します。"
	services="event-hubs"
	documentationCenter=""
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="na"
	ms.tgt_pltfrm="c"
	ms.devlang="java"
	ms.topic="article"
	ms.date="03/08/2016"
	ms.author="sethm"/>

# Event Hubs の使用

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## はじめに

Event Hubs は、拡張性の高いインジェスト システムで、1 秒あたり何百万ものイベントを取り込むことができます。そのためアプリケーションは、接続されているデバイスやアプリケーションによって生成された大量のデータを処理し、分析できます。Event Hubs に収集されたデータは、任意のリアルタイム分析プロバイダーやストレージ クラスターを使用して転送と格納できます。

詳細については、「[Event Hubs の概要]」を参照してください。

このチュートリアルでは、C のコンソール アプリケーションを使用して、イベント ハブにメッセージをインジェストし、Apache Storm を使用して並列で取得する方法を学習します。

このチュートリアルを完了するには、以下が必要です。

+ C の開発環境。このチュートリアルでは、Ubuntu 14.04 での [Azure Linux VM](../virtual-machines/virtual-machines-linux-quick-create-cli.md) 上の GCC スタックを想定しています。他の環境用の手順は、外部リンクで提供されます。

+ [Maven](http://maven.apache.org/) を実行するように構成された Java 開発環境。このチュートリアルでは、[Eclipse](https://www.eclipse.org/) を想定しています。

+ アクティブな Azure アカウントアカウントがない場合は、無料アカウントを数分で作成することができます。詳細については、[Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/)を参照してください。

## Event Hub を作成する

1. [Azure クラシック ポータル]にログオンし、画面の下部にある **[新規]** をクリックします。

2. **[App Services]**、**[Service Bus]**、**[イベント ハブ]**、**[簡易作成]** の順にクリックします。

	![][1]

3. Event Hub の名前を入力して、目的のリージョンを選択し、**[Create a new Event Hub (新しい Event Hub を作成する)]** をクリックします。

	![][2]

4. 先ほど作成した名前空間 (通常は ***event hub name*-ns**) をクリックします。

	![][3]

5. ページ上部にある、**[Event Hubs]]** タブをクリックし、先ほど作成したイベント ハブをクリックします。

	![][4]

6. ページ上部にある **[構成]** タブをクリックし **SendRule** という名前のルールを *Send* 権限で、**ReceiveRule** という別のルールを *Listen* 権限で追加して、**[保存]** をクリックします。

	![][5]

7. 同じページで、**SendRule** と **ReceiveRule** に対して生成されたキーをメモしておきます。

	![][6c]

イベント ハブが作成され、イベントの送受信に必要な接続文字列が手元にあります。

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-c](../../includes/service-bus-event-hubs-get-started-send-c.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## アプリケーションの実行

これで、アプリケーションを実行する準備が整いました。

1.	Eclipse で **LogTopology** クラスを実行し、すべてのパーティションの受信側が起動するまで待機します。

2.	**sender** プログラムを実行し、受信側ウィンドウに表示されるイベントを確認します。

	![][23]

> [AZURE.NOTE] このチュートリアルでは、Storm をローカル モードで開発目的にのみ使用します。Storm のデプロイとパターンの詳細については、「[HDInsight Storm の概要]」と、[Apache Storm] の公式ドキュメントを参照してください。

## 次のステップ

Event Hubs と Storm を統合するアプリケーションの開発には、次のリソースを使用できます。

- 「[HDInsight (Hadoop) での Storm と HBase を使用したセンサー データの分析][]」は、Hadoop クラスター内のセンサー データを取り込むための Event Hubs、Storm、および HBase を使用した完全なシナリオ チュートリアルです。
- 「[HDInsight の Storm で SCP.NET と C# を使用したストリーミング データ処理アプリケーションの開発][]」は、C# を使用して Storm のパイプラインを作成する方法に関するチュートリアルです。

<!-- Images. -->
[1]: ./media/event-hubs-c-storm-getstarted/create-event-hub1.png
[2]: ./media/event-hubs-c-storm-getstarted/create-event-hub2.png
[3]: ./media/event-hubs-c-storm-getstarted/create-event-hub3.png
[4]: ./media/event-hubs-c-storm-getstarted/create-event-hub4.png
[5]: ./media/event-hubs-c-storm-getstarted/create-event-hub5.png
[6]: ./media/event-hubs-getstarted/create-event-hub6.png
[6c]: ./media/event-hubs-c-storm-getstarted/create-event-hub6c.png

[23]: ./media/event-hubs-c-storm-getstarted/receive-storm3.png

<!-- Links -->
[Azure クラシック ポータル]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs の概要]: event-hubs-overview.md

[Apache Storm]: https://storm.incubator.apache.org
[HDInsight Storm の概要]: ../hdinsight/hdinsight-storm-overview.md/
[HDInsight (Hadoop) での Storm と HBase を使用したセンサー データの分析]: ../hdinsight/hdinsight-storm-sensor-data-analysis.md
[HDInsight の Storm で SCP.NET と C# を使用したストリーミング データ処理アプリケーションの開発]: ../hdinsight/hdinsight-storm-develop-csharp-visual-studio-topology.md

<!---HONumber=AcomDC_0413_2016-->