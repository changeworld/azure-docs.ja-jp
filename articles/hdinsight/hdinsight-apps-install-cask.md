---
title: 発行済みアプリケーションのインストール - Datameer - Azure HDInsight
description: Datameer サードパーティ Hadoop アプリケーションをインストールして使用します。
services: hdinsight
author: ashishthaps
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: a8961da0a62815ce60a6ee694d57f3172b81718b
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39592178"
---
# <a name="install-published-application---cask-data-application-platform-cdap"></a>発行済みアプリケーションのインストール - Cask Data Application Platform (CDAP)

この記事では、[CDAP](http://cask.co/products/cdap/) によって発行された Hadoop アプリケーションを Azure HDInsight にインストールして実行する方法について説明します。 HDInsight アプリケーション プラットフォームの概要と、独立系ソフトウェア ベンダー (ISV) によって発行された、利用可能なアプリケーションの一覧については、[サードパーティ Hadoop アプリケーションのインストール](hdinsight-apps-install-applications.md)に関するページを参照してください。 独自のアプリケーションのインストール手順については、[カスタム HDInsight アプリケーションのインストール](hdinsight-apps-install-custom-applications.md)のページを参照してください。

## <a name="about-cdap"></a>CDAP について

Hadoop でのアプリケーションの開発は困難な場合があります。  Hadoop テクノロジには増加し続ける多数の拡張機能があり、統合に時間がかかる可能性があります。 場合によっては、データ フローの監視とメトリックの収集に別個のソリューションの構築が必要です。

### <a name="how-does-cdap-help"></a>CDAP がどのように役立つか

Cask Data Application Platform (CDAP) は、ビッグ データ用の統合プラットフォームです。 CDAP を使用すれば、基盤となるインフラストラクチャではなくアプリケーションの構築に集中できます。

CDAP では、開発者にとって身近な高度な概念と抽象化が使用されます。 これらの抽象化によって内部システムの複雑さが隠され、ソリューションの再利用が促進されます。

[Cask Hydrator](http://cask.co/products/hydrator/) と呼ばれる CDAP 拡張機能は、データ パイプラインを開発して管理するユーザー インターフェイスを提供します。 データ パイプラインは、(データの取得、変換、分析、実行後の操作などの) タスクを実行するさまざまな*プラグインで構成されます。

各 CDAP プラグインには明確に定義されたインターフェイスがあります。そのため、異なるテクノロジを評価するにはあるプラグインを別のものに交換するだけで済み、アプリケーションの他の部分に手を加える必要がありません。

CDAP "*パイプライン*" は、データの高度な視覚的フローをアプリケーションに提供します。 この視覚化では、インジェストからデータ変換と分析を通じて外部データ ストアに至るまで、エンドツーエンドのデータ フローが示されます。

次の例では、データ パイプラインによって Twitter データがリアルタイムで取り込まれ、事前に定義された条件に基づいて一部のツイートが除外されます。 このデータ パイプラインでは、ツイートの生データが変換され、そのデータがよりわかりやすい形式に整形されます。次に、一連の値に応じてツイートがグループ化され、結果が HBase ストアに書き込まれます。

![CDAP パイプライン](./media/hdinsight-apps-install-cask/pipeline.png)

このエンドツーエンドのパイプラインは **Cask Hydrator UI** を使用して構築されます。このプラグインのインターフェイスとドラッグ アンド ドロップ機能を使用して、各ステージの間に接続を作成します。 各プラグインの機能は、個別に分離と変更を行えます。 CDAP を使用すれば、類似のパイプラインを数時間で構築して検証できます。 一般的な Hadoop 環境では、そのようなソリューションの構築には数日かかる場合があります。

CDAP には [Cask Tracker](http://cask.co/products/tracker/) と呼ばれる拡張機能も備わっており、アプリケーション内を流れるデータを視覚的に追跡できます。 Cask Tracker によってシステムに "*データ ガバナンス*" が加わるため、アプリケーション全体でデータ資産が形式的に管理されます。 各データ ポイントの系列を追跡し、関連するメトリックを収集して、プロセス全体のデータの軌跡を監査できます。

次に、上記のパイプラインにおけるデータ フローのようすを示します。

![CDAP トラッカー](./media/hdinsight-apps-install-cask/tracker.png)

## <a name="prerequisites"></a>前提条件

新しい HDInsight クラスター (または既存のクラスター) にこのアプリをインストールするには、次の構成が必要です。

* クラスター レベル: Standard
* クラスターの種類: HBase
* クラスターのバージョン: 3.4、3.5

## <a name="install-the-cdap-published-application"></a>CDAP によって発行されたアプリケーションのインストール

このアプリケーションと利用可能な他の ISV アプリケーションをインストールする詳細な手順については、[サードパーティ Hadoop アプリケーションのインストール](hdinsight-apps-install-applications.md)に関するページを参照してください。

## <a name="launch-cdap"></a>CDAP の起動

1. インストール後、Azure Portal でクラスターから CDAP を起動します。**[設定]** ウィンドウに移動し、**[全般]** カテゴリにある **[アプリケーション]** を選択します。 [インストール済みアプリ] ウィンドウには、インストール済みのアプリケーションがすべて一覧表示されます。

    ![インストール済みの CDAP アプリ](./media/hdinsight-apps-install-cask/cdap-app.png)

2. CDAP を選択すると、Web ページへのリンクと HTTP エンドポイントに加え、SSH エンドポイント パスが表示されます。 [Web ページ] リンクを選択します。

3. 入力を求められたら、クラスターの管理者資格情報を入力します。

    ![Authentication](./media/hdinsight-apps-install-cask/auth.png)

4. サインイン後、Cask CDAP GUI ホーム ページが表示されます。

    ![Cask GUI ホーム ページ](./media/hdinsight-apps-install-cask/gui.png)

5. CDAP インターフェイスを探索して、ページの上部にある **[Cask Market]\(Cask マーケット\)** メニュー リンクをクリックします。

    ![[Cask Market]\(Cask マーケット\) リンク](./media/hdinsight-apps-install-cask/cask-market.png)

6. リストから **[Access Log Sample]\(アクセス ログ サンプル\)** を選択します。

    ![アクセス ログ サンプル](./media/hdinsight-apps-install-cask/market-log-sample.png)

7. **[Load]\(読み込み\)** をクリックして確定します。

    ![[Load]\(読み込み\) をクリック](./media/hdinsight-apps-install-cask/market-load.png)

8. 含まれているサンプル データのビューが表示されます。 **[Next]\(次へ\)** を選択します。

    ![アクセス ログ サンプル - データの表示](./media/hdinsight-apps-install-cask/market-view-data.png)

9. [Destination Type]\(宛先の種類\) として **[Stream]\(ストリーム\)** を選択し、宛先名を入力して **[Finish]\(完了\)** を選択します。

    ![アクセス ログ サンプル - 宛先の選択](./media/hdinsight-apps-install-cask/market-destination.png)

10. データパックが正常に読み込まれたら、**[View Stream Details]\(ストリームの詳細を表示する\)** を選択します。

    ![データパックが正常にアップロードされました](./media/hdinsight-apps-install-cask/market-view-details.png)

11. 名前空間のメタデータを有効にするには、アクセス ログの詳細ページの [Usage]\(使用\) タブにある **[Enable]\(有効化\)** を選択します。

    ![アクセス ログ サンプル - 読み込み済み - メタデータの有効化](./media/hdinsight-apps-install-cask/log-loaded.png)

12. メタデータが有効化されると、監査メッセージ情報を示すグラフが表示されます。

    ![アクセス ログ サンプル - 有効化されたメタデータ](./media/hdinsight-apps-install-cask/log-metadata.png)

13. ログ データを探索するには、ページの上部にある **[Explore]\(探索\)** アイコンを選択します。

    ![アクセス ログ サンプル - 探索](./media/hdinsight-apps-install-cask/log-explore.png)

14. サンプル SQL クエリが表示されます。 必要に応じて自由に変更し、**[Execute]\(実行\)** を選択します。

    ![アクセス ログ サンプル - クエリでデータセットを探索](./media/hdinsight-apps-install-cask/log-query.png)

15. クエリが完了したら、[Actions]\(アクション\) 列にある **[View]\(表示\)** アイコンを選択します。

    ![アクセス ログ サンプル - 完了したクエリの表示](./media/hdinsight-apps-install-cask/log-query-view.png)

16. クエリの結果が表示されます。

    ![アクセス ログ サンプル - クエリの結果](./media/hdinsight-apps-install-cask/log-query-results.png)

## <a name="next-steps"></a>次の手順

* [Cask ドキュメント](http://cask.co/resources/documentation/)。
* [カスタム HDInsight アプリケーションをインストールする](hdinsight-apps-install-custom-applications.md): 未発行の HDInsight アプリケーションを HDInsight にデプロイする方法について確認します。
* [HDInsight アプリケーションを発行する](hdinsight-apps-publish-applications.md): カスタム HDInsight アプリケーションを Azure Marketplace に発行する方法について確認します。
* [MSDN: HDInsight アプリケーションをインストールする](https://msdn.microsoft.com/library/mt706515.aspx): HDInsight アプリケーションを定義する方法について確認します。
* [スクリプト アクションを使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md): スクリプト アクションを使用してアプリケーションを追加インストールする方法を確認します。
* [HDInsight で空のエッジ ノードを使用する](hdinsight-apps-use-edge-node.md): HDInsight クラスターへのアクセスのほか、HDInsight アプリケーションのテストとホストに空のエッジ ノードを使用する方法を確認します。
