---
title: "HDInsight で Storm を使用して Event Hubs のイベントを処理する | Microsoft Docs"
description: "Visual Studio で HDInsight Tools for Visual Studio を使用して作成した C# Storm トポロジによって Event Hubs のデータを処理する方法について説明します。"
services: hdinsight,notification hubs
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 67f9d08c-eea0-401b-952b-db765655dad0
ms.service: hdinsight
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/27/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 77d0dfe7e09baab9b923b3c49af0cb7c28cd8625


---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-c"></a>HDInsight で Storm を使用して Azure Event Hubs のイベントを処理する (＃C)
Azure Event Hubs では、Web サイト、アプリ、デバイスで発生する大量のデータを処理できます。 Event Hubs スパウトでは、HDInsight で Apache Storm を使用してこのデータをリアルタイムで簡単に分析できます。 また、Event Hubs のボルトを使用して Storm から Event Hub にデータを書き込むこともできます。

このチュートリアルでは、HDInsight Tools for Visual Studio と共にインストールされる Visual Studio テンプレートを使用して、Azure Event Hubs で動作する 2 つのトポロジを作成する方法を説明します。

* **EventHubWriter**: データをランダムに生成して Event Hubs に書き込む
* **EventHubReader**: Event Hubs からデータを読み取り、Storm ログにデータを記録する

> [!NOTE]
> このドキュメントの手順は Visual Studio を使う Windows 開発環境でのものですが、コンパイル済みのプロジェクトは、Linux または Windows ベースの HDInsight クラスターに送信できます。 SCP.NET トポロジをサポートする Linux ベースのクラスターは、2016 年 10 月 28 日より後に作成されたものだけです。
> 
> C# トポロジを Linux ベースのクラスターで使うには、プロジェクトによって使われる Microsoft.SCP.Net.SDK NuGet パッケージをバージョン 0.10.0.6 以降に更新する必要があります。 また、パッケージのバージョンは、HDInsight にインストールされている Storm のメジャー バージョンと一致する必要もあります。 たとえば、HDInsight バージョン 3.3 および 3.4 上の Storm は Storm バージョン 0.10.x を使いますが、HDInsight 3.5 は Storm 1.0.x を使います。
> 
> Linux ベースのクラスターの C# トポロジは、.NET 4.5 を使い、Mono を使って HDInsight クラスターで実行する必要があります。 ほとんどの機能は動作しますが、[Mono の互換性](http://www.mono-project.com/docs/about-mono/compatibility/)のドキュメントで可能性のある非互換性について確認する必要があります。
> 
> このプロジェクトの Linux または Windows ベースのクラスターで機能する Java バージョンについては、「[HDInsight で Storm を使用して Azure Event Hubs のイベントを処理する (＃C)](hdinsight-storm-develop-java-event-hub-topology.md)」をご覧ください。
> 
> 

## <a name="prerequisites"></a>前提条件
* [HDInsight クラスターでの Apache Storm](hdinsight-apache-storm-tutorial-get-started.md)
* [Azure Event Hub](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
* [Azure .NET SDK](http://azure.microsoft.com/downloads/)
* [HDInsight Tools for Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md)

## <a name="completed-project"></a>完成したプロジェクト
このチュートリアルで作成したプロジェクトの完全なバージョンを GitHub からダウンロードできます ( [eventhub-storm-hybrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub))。 ただし、このチュートリアルの手順に従って構成設定を指定する必要があります。

## <a name="event-hubs-spout-and-bolt"></a>Event Hubs スパウトとボルト
Event Hubs スパウトとボルトは Java コンポーネントで、これらのコンポーネントを使用して Apache Storm から Event Hubs を簡単に操作できます。 これらのコンポーネントは Java で記述されていますが、HDInsight Tools for Visual Studio を使用して、C# と Java のコンポーネントが混在するハイブリッド トポロジを作成できます。

スパウトとボルトは、**eventhubs-storm-spout-#.#-jar-with-dependencies.jar**という名前の 1 つの Java アーカイブ (.jar) ファイルとして配布されます。

### <a name="download-the-jar-file"></a>jar ファイルのダウンロード
jar ファイルの最新バージョンは、**lib/eventhubs** フォルダーの [HDInsight Storm examples](https://github.com/hdinsight/hdinsight-storm-examples) プロジェクトに含まれています。 ファイルをダウンロードするには、次のいずれかの方法を実行してください。

> [!NOTE]
> スパウトとボルトは Apache Storm プロジェクトに組み込むために送信されています。 詳細については、GitHub の「 [STORM-583: Initial check-in for storm-event hubs (STORM-583: Storm-event hub の初期チェックイン)](https://github.com/apache/storm/pull/336/files) 」を参照してください。
> 
> 

* **ZIP ファイルをダウンロードする**: [HDInsight Storm examples](https://github.com/hdinsight/hdinsight-storm-examples) サイトの右側のウィンドウで **[Download ZIP]** を選択し、プロジェクトが含まれている .zip ファイルをダウンロードします。
  
    ![ZIP のダウンロード ボタン](./media/hdinsight-storm-develop-csharp-event-hub-topology/download.png)
  
    ファイルをダウンロードした後に、アーカイブを抽出できます。ファイルは **lib** ディレクトリに保存されます。
* **プロジェクトを複製する**: [Git](http://git-scm.com/) をインストール済みの場合は、次のコマンドを使用してリポジトリをローカルに複製し、**lib** ディレクトリにあるファイルを見つけます。
  
        git clone https://github.com/hdinsight/hdinsight-storm-examples

## <a name="configure-event-hubs"></a>Event Hubs を構成する
Event Hubs は、この例のデータ ソースです。 「[Event Hubs の使用](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)」ページの「**Event Hub を作成する**」セクションの情報を使用します。

1. Event Hub が作成されたら、Azure Portal の EventHub ブレードを表示し、**[共有アクセス ポリシー]** を選択します。 **[+ 追加]** エントリを使用して、次のポリシーを追加します。
   
   | 名前 | アクセス許可 |
   | --- | --- |
   | ライター |送信 |
   | リーダー |リッスン |
   
    ![ポリシー](./media/hdinsight-storm-develop-csharp-event-hub-topology/sas.png)
2. **reader** ポリシーと **writer** ポリシーを選択します。 両方のポリシーの **PRIMARY KEY** 値をコピーして保存します (これらは後で使用します)。

## <a name="configure-the-eventhubwriter"></a>EventHubWriter の構成
1. HDInsight Tools for Visual Studio の最新バージョンをまだインストールしていない場合は、「[HDInsight Tools for Visual Studio の使用開始](hdinsight-hadoop-visual-studio-tools-get-started.md)」をご覧ください。
2. [eventhub-storm-hybrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub) からソリューションをダウンロードします。 ソリューションを開き、コード全体をよく見て **EventHubWriter** プロジェクトを確認します。
3. **EventHubWriter** プロジェクトで、**App.config** ファイルを開きます。 以前に構成した Event Hub の情報を使用して、次のキーの値を入力します。
   
   | キー | 値 |
   | --- | --- |
   | EventHubPolicyName |writer (*Send* 権限を持つポリシーに別の名前を使用した場合は、その名前を使用) |
   | EventHubPolicyKey |writer ポリシーのキー |
   | EventHubNamespace |Event Hub が含まれている名前空間 |
   | EventHubName |Event Hub 名 |
   | EventHubPartitionCount |Event Hub のパーティションの数 |
4. **App.config** ファイルを保存して閉じます。

## <a name="configure-the-eventhubreader"></a>EventHubReader の構成
1. **EventHubReader** プロジェクトを開き、コード全体をよく見ます。
2. **EventHubWriter**の **App.config** を開きます。 以前に構成した Event Hub の情報を使用して、次のキーの値を入力します。
   
   | キー | 値 |
   | --- | --- |
   | EventHubPolicyName |reader (*listen* 権限を持つポリシーに別の名前を使用した場合は、その名前を使用) |
   | EventHubPolicyKey |reader ポリシーのキー |
   | EventHubNamespace |Event Hub が含まれている名前空間 |
   | EventHubName |Event Hub 名 |
   | EventHubPartitionCount |Event Hub のパーティションの数 |
3. **App.config** ファイルを保存して閉じます。

## <a name="deploy-the-topologies"></a>トポロジのデプロイ
1. **ソリューション エクスプローラー**で **EventHubReader** プロジェクトを右クリックし、**[HDInsight での Storm に送信]** を選択します。
   
    ![Storm に送信](./media/hdinsight-storm-develop-csharp-event-hub-topology/submittostorm.png)
2. **[トポロジの送信]** 画面で該当する **[Storm クラスター]** を選択します。 **[追加の構成]** を展開し、**[Java ファイル パス]**、**[...]** の順に選択し、前の手順でダウンロードした **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** ファイルがあるディレクトリを選択します。 最後に、 **[送信]**をクリックします。
   
    ![送信ダイアログの画像](./media/hdinsight-storm-develop-csharp-event-hub-topology/submit.png)
3. トポロジが送信されると、 **[Storm トポロジ ビューアー]** が表示されます。 ダイアログの左側にある **EventHubReader** トポロジを選択し、トポロジの統計情報を表示します。 Event Hubs に書き込まれたイベントはまだないため、現時点では、何も発生していません。
   
    ![ストレージ ビューの例](./media/hdinsight-storm-develop-csharp-event-hub-topology/topologyviewer.png)
4. **ソリューション エクスプローラー**で **EventHubWriter** プロジェクトを右クリックし、**[HDInsight での Storm に送信]** を選択します。
5. **[トポロジの送信]** 画面で該当する **[Storm クラスター]** を選択します。 **[追加の構成]** を展開し、**[Java ファイル パス]**、**[...]** の順に選択し、前の手順でダウンロードした **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** ファイルがあるディレクトリを選択します。 最後に、 **[送信]**をクリックします。
6. トポロジが送信されたら、 **[Storm トポロジ ビューアー]** でトポロジ一覧を最新情報に更新し、両方のトポロジがクラスターで実行中であることを確認します。
7. **[Storm トポロジ ビューアー]** で **EventHubReader** トポロジを選択します。
8. グラフ ビューで、**LogBolt** コンポーネントをダブルクリックします。 これによって、ボルトの **[コンポーネントの概要]** ページが開きます。
9. **[Executors]** セクションで、**[ポート]** 列のリンクを 1 つ選択します。 これによって、コンポーネントで記録された情報が表示されます。 次のような情報が記録されています。
   
        2016-10-20 13:26:44.186 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:7, stream: default, id: {5769732396213255808=520853934697489134}, [{"deviceId":3,"deviceValue":1379915540}]
        2016-10-20 13:26:44.234 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:7, stream: default, id: {7154038361491319965=4543766486572976404}, [{"deviceId":3,"deviceValue":459399321}]
        2016-10-20 13:26:44.335 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:6, stream: default, id: {513308780877039680=-7571211415704099042}, [{"deviceId":5,"deviceValue":845561159}]
        2016-10-20 13:26:44.445 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:7, stream: default, id: {-2409895457033895206=5479027861202203517}, [{"deviceId":8,"deviceValue":2105860655}]

## <a name="stop-the-topologies"></a>トポロジの停止
トポロジを停止するには、**[Storm トポロジ ビューアー]** で各トポロジを選択し、**[強制終了]** をクリックします。

![トポロジの強制終了の画像](./media/hdinsight-storm-develop-csharp-event-hub-topology/killtopology.png)

## <a name="delete-your-cluster"></a>クラスターを削除する
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="notes"></a>メモ
### <a name="checkpointing"></a>チェックポイント機能
EventHubSpout は Zookeeper ノードに対する状態へのチェックポイントを定期的に設定し、キューから読み取ったメッセージの現在のオフセットを保存します。 これにより、コンポーネントは次のシナリオで保存されたオフセットでメッセージの受信を開始できます。

* コンポーネントのインスタンスは失敗し、再起動されます。
* ノードを追加または削除して、クラスターを拡張または圧縮します。
* トポロジが強制終了され、 **同じ名前で**再起動されます。

また、保存されたチェックポイントを WASB (HDInsight クラスターで使用される Azure のストレージ) にエクスポート、インポートできます。これを実行するスクリプトは、**c:\apps\dist\storm-0.9.3.2.2.1.0-2340\zkdatatool-1.0\bin** の HDInsight クラスター上の Storm に配置されています。

> [!NOTE]
> クラスターにインストールされた Storm のバージョンは今後変更される可能性があるため、パスのバージョン番号は異なる場合があります。
> 
> 

このディレクトリのスクリプトは次のとおりです。

* **stormmeta_import.cmd**: クラスターの既定のストレージ コンテナーから Zookeeper にすべての Storm メタデータをインポートします。
* **stormmeta_export.cmd**: Zookeeper からクラスターの既定のストレージ コンテナーにすべての Storm メタデータをエクスポートします。
* **stormmeta_delete.cmd**: Zookeeper からのすべての Storm メタデータを削除します。

インポートのエクスポートにより、クラスターを削除する必要がある一方で、新しいクラスターを再びオンラインにするときにハブの現在のオフセットから処理を再開する場合、チェックポイントのデータを保持できます。

> [!NOTE]
> データは既定のストレージ コンテナーに保存されるため、新しいクラスターで以前のクラスターと同じストレージ アカウントとコンテナーを使用する **必要があります** 。
> 
> 

## <a name="next-steps"></a>次のステップ
このドキュメントでは、C# トポロジから Java Event Hubs スパウトおよびボルトを使用して、Azure Event Hub のデータを操作する方法について説明しました。 C# トポロジの作成の詳細については、次の記事を参照してください。

* [Visual Studio を使用して HDInsight で Apache Storm の C# トポロジを開発する](hdinsight-storm-develop-csharp-visual-studio-topology.md)
* [SCP プログラミング ガイド](hdinsight-storm-scp-programming-guide.md)
* [HDInsight 上の Storm に関するトポロジ例](hdinsight-storm-example-topology.md)




<!--HONumber=Nov16_HO3-->


