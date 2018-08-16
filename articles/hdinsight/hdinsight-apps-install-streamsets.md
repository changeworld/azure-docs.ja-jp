---
title: 発行済みアプリケーションのインストール - StreamSets Data Collector - Azure HDInsight
description: StreamSets Data Collector サードパーティ Hadoop アプリケーションをインストールして使用します。
services: hdinsight
author: ashishthaps
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: c0b458b19abb707305ca609fbd5bfac63c92567e
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39591229"
---
# <a name="install-published-application---streamsets-data-collector"></a>発行済みアプリケーションのインストール - StreamSets Data Collector

この記事では、[HDInsight 用の StreamSets Data Collector](https://streamsets.com/) の発行済み Hadoop アプリケーションを Azure HDInsight にインストールして実行する方法について説明します。 HDInsight アプリケーション プラットフォームの概要と、独立系ソフトウェア ベンダー (ISV) によって発行された、利用可能なアプリケーションの一覧については、[サードパーティ Hadoop アプリケーションのインストール](hdinsight-apps-install-applications.md)に関するページを参照してください。 独自のアプリケーションのインストール手順については、[カスタム HDInsight アプリケーションのインストール](hdinsight-apps-install-custom-applications.md)のページを参照してください。

## <a name="about-streamsets-data-collector"></a>StreamSets Data Collector について

StreamSets Data Collector は、Azure HDInsight アプリケーションの上にデプロイされます。 StreamSets Data Collector はフル機能を備えた統合開発環境 (IDE) を備えており、任意の環境間の取り込みパイプラインを設計、テスト、デプロイ、管理できます。 これらのパイプラインでは、ストリーム データとバッチ データを調和させることができるほか、ストリーム内のさまざまな変換を含めることができます。どの処理でも、カスタム コードを記述する必要はありません。

StreamSets Data Collector では、HDFS、Kafka、Solr、Hive、HBASE、Kudu などの多数のビッグ データ コンポーネントを使用して、データ フローを作成できます。 StreamSets Data Collector がエッジ サーバー (または Hadoop クラスター) で実行されていると、データの異常とデータ フロー操作の両方をリアルタイムで監視できます。 この監視には、しきい値ベースのアラート、異常検出、エラー レコードの自動修復が含まれます。

StreamSets Data Collector は、パイプラインの各ステージを論理的に分離するよう設計されています。そのため、手軽に新しいプロセッサとコネクタを使用して新しいビジネス要件を満たすことができます。コーディングは不要で、最小限のダウンタイムで済みます。

### <a name="streamsets-resource-links"></a>StreamSets のリソース リンク

* [ドキュメント](https://streamsets.com/documentation/datacollector/latest/help/#Getting_Started/GettingStarted_Title.html)
* [ブログ](https://streamsets.com/blog/)
* [チュートリアル](https://github.com/streamsets/tutorials)
* [開発者サポート フォーラム](https://groups.google.com/a/streamsets.com/forum/#!forum/sdc-user)
* [StreamSets の Slack パブリック チャンネル](https://streamsetters.slack.com/)
* [ソース コード](https://github.com/streamsets)

## <a name="prerequisites"></a>前提条件

新しい HDInsight クラスター (または既存のクラスター) にこのアプリをインストールするには、次の構成が必要です。

* クラスター レベル: Standard または Premium
* クラスターのバージョン: 3.5 以上

## <a name="install-the-streamsets-data-collector-published-application"></a>StreamSets Data Collector によって発行されたアプリケーションのインストール

このアプリケーションと利用可能な他の ISV アプリケーションをインストールする詳細な手順については、[サードパーティ Hadoop アプリケーションのインストール](hdinsight-apps-install-applications.md)に関するページを参照してください。

## <a name="launch-streamsets-data-collector"></a>StreamSets Data Collector の起動

1. インストール後、Azure Portal でクラスターから StreamSets を起動できます。**[設定]** ウィンドウに移動し、**[全般]** カテゴリにある **[アプリケーション]** を選択します。 [インストール済みアプリ] ウィンドウには、インストール済みのアプリケーションが一覧表示されます。

    ![インストールされた StreamSets アプリ](./media/hdinsight-apps-install-streamsets/streamsets.png)

2. StreamSets Data Collector を選択すると、Web ページへのリンクと SSH エンドポイント パスが表示されます。 [Web ページ] リンクを選択します。

3. ログイン ダイアログ ボックスで、`admin` と `admin` の資格情報を使用してログインします。

4. [Get Started]\(開始\) ページで **[Create New Pipeline]\(新しいパイプラインの作成\)** をクリックします。

    ![新しいパイプラインの作成](./media/hdinsight-apps-install-streamsets/get-started.png)

5. 新しいパイプラインのウィンドウでパイプラインの名前 ("Hello World") を入力し、必要に応じて説明を入力して、**[Save]\(保存\)** を選択します。

6. Data Collector コンソールが表示されます。 プロパティ パネルには、パイプラインのプロパティが表示されます。
 
    ![Data Collector コンソール](./media/hdinsight-apps-install-streamsets/pipeline-canvas.png)

7. これで、[StreamSets チュートリアル](https://streamsets.com/documentation/datacollector/latest/help/#Tutorial/Tutorial-title.html)を実行する準備が整いました。 このチュートリアルでは、最初のパイプラインを作成する詳細な手順について説明されています。

## <a name="next-steps"></a>次の手順

* [StreamSets Data Collector のドキュメント](https://streamsets.com/documentation/datacollector/latest/help/#Getting_Started/GettingStarted_Title.html#concept_htw_ghg_jq)。
* [カスタム HDInsight アプリケーションをインストールする](hdinsight-apps-install-custom-applications.md): 未発行の HDInsight アプリケーションを HDInsight にデプロイする方法について確認します。
* [HDInsight アプリケーションを発行する](hdinsight-apps-publish-applications.md): カスタム HDInsight アプリケーションを Azure Marketplace に発行する方法について確認します。
* [MSDN: HDInsight アプリケーションをインストールする](https://msdn.microsoft.com/library/mt706515.aspx): HDInsight アプリケーションを定義する方法について確認します。
* [スクリプト アクションを使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md): スクリプト アクションを使用してアプリケーションを追加インストールする方法を確認します。
* [HDInsight で空のエッジ ノードを使用する](hdinsight-apps-use-edge-node.md): HDInsight クラスターへのアクセスのほか、HDInsight アプリケーションのテストとホストに空のエッジ ノードを使用する方法を確認します。
