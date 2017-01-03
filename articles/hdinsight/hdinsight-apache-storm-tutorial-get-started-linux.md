---
title: "Apache Storm チュートリアル: HDInsight での Linux ベースの Storm の使用 | Microsoft Docs"
description: "Linux ベースの HDInsight での Apache Storm および Storm Starter サンプルを使用したビッグ データ分析の概要 Storm を使用してデータをリアルタイムに処理する方法について説明します。"
keywords: "Apache Storm, Apache Storm チュートリアル, ビッグ データの分析, Storm Starter"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: d710dcac-35d1-4c27-a8d6-acaf8146b485
ms.service: hdinsight
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/18/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: ec3384dd68a28117cc00de20e4c08ad0ccd67bad
ms.openlocfilehash: a7516a98cbe25f9ed458131083a18899d3d50004


---
# <a name="apache-storm-tutorial-get-started-with-the-storm-starter-samples-for-big-data-analytics-on-hdinsight"></a>Apache Storm チュートリアル: Storm Starter サンプルを使用した HDInsight でのビッグ データ分析の概要

Apache Storm は、データ ストリームの処理を目的とし、スケーラビリティとフォールト トレランスに優れた、分散型のリアルタイム計算システムです。 Azure HDInsight の Storm を使用して、Storm でリアルタイムで ビッグ データ分析を実行するクラウドベースの Storm クラスターを作成できます。

> [!NOTE]
> この記事の手順では、Linux ベースの HDInsight クラスターを作成します。 HDInsight クラスターで Windows ベースの Storm を作成する手順については、「 [Apache Storm チュートリアル: Storm Starter サンプルを使用した HDInsight でのビッグ データ分析の概要](hdinsight-apache-storm-tutorial-get-started.md)

## <a name="prerequisites"></a>前提条件

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Apache Storm チュートリアルを正常に完了するには、次の条件を満たす必要があります。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。

* **SSH と SCP を熟知していること**。 HDInsight での SSH と SCP の使用方法の詳細については、次の記事をご覧ください。
  
    * **Linux、Unix、または OS X クライアント**: [HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](hdinsight-hadoop-linux-use-ssh-unix.md)
    
    * **Windows クライアント**: [HDInsight の Linux ベースの Hadoop で Windows から SSH (PuTTY) を使用する](hdinsight-hadoop-linux-use-ssh-windows.md)

### <a name="access-control-requirements"></a>アクセス制御の要件

[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-a-storm-cluster"></a>Storm クラスターを作成する

このセクションでは、Azure Resource Manager テンプレートを使用して HDInsight バージョン 3.5 クラスター (Storm バージョン 1.0.1) を作成します。 HDInsight バージョンとその SLA については、「 [HDInsight コンポーネントのバージョン](hdinsight-component-versioning.md)」をご覧ください。 その他のクラスター作成方法については、「 [HDInsight での Linux ベースの Hadoop クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)」を参照してください。

1. 次の画像をクリックして Azure ポータルでテンプレートを開きます。         
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-storm-cluster-in-hdinsight-35.json" target="_blank"><img src="./media/hdinsight-apache-storm-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    テンプレートはパブリック BLOB コンテナー (*https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-storm-cluster-in-hdinsight.json*) 内にあります。 

2. __[カスタム デプロイ]__ ブレードで以下を入力します。
   
    * __[リソース グループ]__: クラスターが作成されるリソース グループ。

    * **[クラスター名]**: Hadoop クラスターの名前。

    * __[Cluster login name (クラスター ログイン名)]__ と __[パスワード]__: 既定のログイン名は admin です。
    
    * __[SSH ユーザー名]__ と __[パスワード]__: SSH を使用してクラスターに接続するためのユーザーとパスワード。

    * __[場所]__: クラスターの地理的な場所。
     
     これらの値を書き留めておいてください。  この情報は後で必要になります。
     
     > [!NOTE]
     > SSH はコマンドラインで HDInsight クラスターにリモート アクセスするために使用されます。 ここで使用するユーザー名とパスワードは、SSH でクラスターに接続するときに使用されます。 また、SSH ユーザー名は一意にする必要があります。この名前により、すべての HDInsight クラスター ノードでユーザー アカウントが作成されます。 次はクラスターのサービスのために予約されている名前の一部であり、SSH ユーザー名として使用できません。
     > 
     > root、hdiuser、storm、hbase、ubuntu、zookeeper、hdfs、yarn、mapred、hbase、hive、oozie、falcon、sqoop、admin、tez、hcat、hdinsight-zookeeper
     > 
     > HDInsight での SSH の使用方法の詳細については、次の記事を参照してください。
     > 
     > * [HDInsight の Linux ベースの Hadoop で SSH を使用する](hdinsight-hadoop-linux-use-ssh-unix.md)
     > * [HDInsight の Linux ベースの Hadoop で Windows から SSH (PuTTY) を使用する](hdinsight-hadoop-linux-use-ssh-windows.md)

3. __[上記の使用条件に同意する]__ を選択し、**[OK]** をクリックして、__[Pin to dashbaord (ダッシュボードにピン留め)]__ を選択します。

6. **[購入]** をクリックします。 "Submitting deployment for Template deployment" という新しいタイルが表示されます。 クラスターの作成には約 20 分かかります。

## <a name="run-a-storm-starter-sample-on-hdinsight"></a>HDInsight での Storm Starter サンプルの実行

[storm-starter](https://github.com/apache/storm/tree/master/examples/storm-starter) の例は、HDInsight クラスターに含まれています。 次の手順では、WordCount の例を実行します。

1. SSH を使用して HDInsight クラスターに接続します。
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    SSH ユーザー アカウントを保護するためにパスワードを使用している場合は、パスワードの入力を求められます。 公開キーを使用している場合、 `-i` パラメーターを使用して、対応する秘密キーを指定することが必要な場合があります。 たとえば、「 `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`」のように入力します。
   
    Linux ベースの HDInsight での SSH の使用方法の詳細については、次の記事を参照してください。
   
    * [HDInsight の Linux ベースの Hadoop で SSH を使用する](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [HDInsight の Linux ベースの Hadoop で Windows から SSH (PuTTY) を使用する](hdinsight-hadoop-linux-use-ssh-windows.md)

2. 次のコマンドを実行してトポロジの例を開始します。
   
        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar storm jar org.apache.storm.starter.WordCountTopology wordcount
   
    > [!NOTE]
    > 以前のバージョンの HDInsight では、トポロジのクラス名は `org.apache.storm.starter.WordCountTopology` ではなく `storm.starter.WordCountTopology` です。
   
    クラスター上で、’wordcount’ というフレンドリ名の WordCount トポロジの例が開始されます。 文はランダムに生成され、文中の各単語の出現回数がカウントされます。
   
    > [!NOTE]
    > 独自のトポロジをクラスターに送信する場合、まずクラスターを含む jar ファイルをコピーしてから、`storm` コマンドを実行します。 この場合、ファイルが保存されているクライアントから `scp` コマンドを実行します。 たとえば、 `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
    > 
    > WordCount の例と他の Storm スターターの例は、 `/usr/hdp/current/storm-client/contrib/storm-starter/`のクラスターに既に含まれています。

Storm スターターの例のコードを確認したい場合、そのコードは [https://github.com/apache/storm/tree/1.0.x-branch/examples/storm-starter](https://github.com/apache/storm/tree/1.0.x-branch/examples/storm-starter) で見つかります。 このリンクは、HDInsight 3.5 に付属している Storm 1.0.x 用です。 Storm の他のバージョンについては、このページの上部にある __[Branch (分岐)]__ ボタンを使用して、別の Storm バージョンを選択してください。

## <a name="monitor-the-topology"></a>トポロジの監視

Storm UI には、トポロジの実行を操作する Web インターフェイスがあり、HDInsight クラスターに含まれています。

次の手順により、Storm UI を使用してトポロジを監視します。

1. Web ブラウザーを開いて https://CLUSTERNAME.azurehdinsight.net/stormui を参照します。**CLUSTERNAME** は実際のクラスターの名前です。 Storm UI が表示されます。
    
    > [!NOTE]
    > ユーザー名とパスワードの入力が求められたら、クラスターの作成時に使用したクラスター管理者名 (admin) とパスワードを入力します。

2. **[トポロジの概要]** で、**[名前]** 列の **[wordcount]** エントリを選択します。 これにより、トポロジの詳細が表示されます。
    
    ![Storm Starter WordCount トポロジの情報が含まれている Storm ダッシュボード。](./media/hdinsight-apache-storm-tutorial-get-started-linux/topology-summary.png)
    
    このページには、次の情報が表示されます。
    
    * **トポロジの統計** - 時間枠で整理された、トポロジのパフォーマンスに関する基本的な情報。
     
        > [!NOTE]
        > 特定の時間枠を選択すると、ページの他のセクションに表示される情報の時間枠に変更されます。

    * **スパウト** - 各スパウトによって返された最後のエラーを含む、スパウト関する基本的な情報。
    
    * **ボルト** - ボルトに関する基本的な情報。
    
    * **トポロジの構成** - トポロジの構成に関する詳細情報。
     
    このページには、トポロジで実行できるアクションも表示されます。
   
    * **アクティブ化** - アクティブ化が解除されたトポロジの処理を再開します。
    
    * **アクティブ化の解除** - 実行中のトポロジを一時停止します
    
    * **再調整** - トポロジの並列処理を調整します。 クラスターのノード数を変更した場合は、実行中のトポロジを再調整する必要があります。 この操作で、クラスター内のノード数の増減に合わせて、トポロジの並列処理を調整できます。 詳細については、「 [Understanding the parallelism of a Storm topology (Storm トポロジの並列処理)](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)」を参照してください。
    
    * **強制終了** - 指定したタイムアウト後に Storm トポロジを停止します。

3. このページで、**[スパウト]** または **[ボルト]** セクションからエントリを選択します。 選択したコンポーネントに関する情報が表示されます。
   
    ![選択したコンポーネントに関する情報が含まれている Storm ダッシュボード。](./media/hdinsight-apache-storm-tutorial-get-started-linux/component-summary.png)
   
    このページには次の情報が表示されます。
   
    * **スパウト / ボルトの統計** - 時間枠で整理された、コンポーネントのパフォーマンスに関する基本的な情報。
     
        > [!NOTE]
        > 特定の時間枠を選択すると、ページの他のセクションに表示される情報の時間枠に変更されます。
     
    * **入力の統計** (ボルトのみ) - ボルトによって使用されるデータを生成するコンポーネントに関する情報。
    
    * **出力の統計** - このボルトによって出力されるデータに関する情報。
    
    * **エグゼキュータ** - このコンポーネントのインスタンスに関する情報。
    
    * **エラー** - このコンポーネントで生成されたエラー。

4. スパウトまたはボルトの詳細を表示したら、**[エグゼキュータ]** セクションの **[ポート]** 列でエントリを選択して、コンポーネントの特定のインスタンスの詳細を表示します。
   
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]
   
    このデータでは、 **seven** という単語が 1493957 回発生したことを確認できます。 これは、このトポロジが開始されてから発生した回数です。

## <a name="stop-the-topology"></a>トポロジを停止する

ワードカウント トポロジの **[トポロジの概要]** ページに戻り、**[トポロジのアクション]** セクションで **[強制終了]** ボタンを選択します。 メッセージが表示されたら、トポロジを停止するまでの待機秒数として「10」を入力します。 タイムアウト期間後は、ダッシュボードの **[Storm UI]** セクションにアクセスしても、トポロジは表示されません。

## <a name="delete-the-cluster"></a>クラスターを削除する

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="a-idnextanext-steps"></a><a id="next"></a>次のステップ

この Apache Storm チュートリアルでは、Storm Starter を使用して、HDInsight クラスターで Storm を作成する方法と、Storm ダッシュボードを使用して Storm トポロジをデプロイ、監視、管理する方法について説明しました。 次は、 [Maven を使用して Java ベースのトポロジを開発](hdinsight-storm-develop-java-topology.md)する方法について説明します。

既に Java ベースのトポロジの開発経験があり、既存のトポロジを HDInsight にデプロイする方法をお探しの方は、 [HDInsight での Apache Storm トポロジのデプロイと管理](hdinsight-storm-deploy-monitor-topology-linux.md)に関するページを参照してください。

.NET 開発者は、Visual Studio を使用して C# トポロジまたは C#/Java ハイブリッド トポロジを作成できます。 詳細については、「[Hadoop Tools for Visual Studio を使用した HDInsight での Apache Storm の C# トポロジの開発](hdinsight-storm-develop-csharp-visual-studio-topology.md)」をご覧ください。

HDInsight 上の Storm で使用できるトポロジの例については、以下の例をご覧ください。

* [HDInsight 上の Storm に関するトポロジ例](hdinsight-storm-example-topology.md)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/
[hdinsight-provision]: hdinsight-provision-clusters.md
[preview-portal]: https://portal.azure.com/



<!--HONumber=Dec16_HO3-->


