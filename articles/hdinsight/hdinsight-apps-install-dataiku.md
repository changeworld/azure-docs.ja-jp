---
title: 発行済みアプリケーションのインストール - Dataiku DSS - Azure HDInsight
description: Dataiku DSS サードパーティ Hadoop アプリケーションをインストールして使用します。
services: hdinsight
author: ashishthaps
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: 8d01b94ce4af6e3f5bcdc05ce84ef6deb1d3cae6
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39598773"
---
# <a name="install-published-application---dataiku-dds"></a>発行済みアプリケーションのインストール - Dataiku DSS

この記事では、[Dataiku DSS](https://www.dataiku.com/) によって発行された Hadoop アプリケーションを Azure HDInsight にインストールして実行する方法について説明します。 HDInsight アプリケーション プラットフォームの概要と、独立系ソフトウェア ベンダー (ISV) によって発行された、利用可能なアプリケーションの一覧については、[サードパーティ Hadoop アプリケーションのインストール](hdinsight-apps-install-applications.md)に関するページを参照してください。 独自のアプリケーションのインストール手順については、[カスタム HDInsight アプリケーションのインストール](hdinsight-apps-install-custom-applications.md)のページを参照してください。

## <a name="about-dataiku-dss"></a>Dataiku DSS について

Dataiku [Data Science Studio (DSS)](https://www.dataiku.com/dss/features/connectivity/) とは、データ サイエンティストが分析ソリューションを構築して提供できる共同データ サイエンス プラットフォームです。 DSS を HDInsight アプリケーションとして提供すると、データ サイエンスを使用してビッグ データ ソリューションを構築し、それらをエンタープライズのグレードとスケールで実行できます。

DSS を使用して、データ インジェスト、準備、処理から始まる完全な分析ソリューションを実装できます。 DSS ソリューションには、トレーニングおよび応用機械学習モデル、視覚化、運用化を含めることもできます。

DSS は、Hadoop クラスターまたは Spark クラスターを使用して HDInsight にインストールできます。 DSS は既存の実行中のクラスターにインストールできるほか、新しいクラスターの作成時にインストールできます。 また、DSS では、Azure Blob Storage をデータの読み取りのコネクタとして使用することもできます。

DSS を使用してプロジェクトを作成し、そのプロジェクトで MapReduce ジョブまたは Spark ジョブを生成できます。 これらのジョブは、通常の MapReduce ジョブまたは Spark ジョブとして HDInsight で実行されます。そのため、必要に応じてクラスターをスケーリングできます。

## <a name="prerequisites"></a>前提条件

新しい HDInsight クラスター (または既存のクラスター) にこのアプリをインストールするには、次の構成が必要です。

* クラスター レベル: Standard、Premium
* クラスターの種類: Hadoop、Spark
* クラスターのバージョン: 3.4、3.5

## <a name="install-the-dataiku-dss-published-application"></a>Dataiku DSS によって発行されたアプリケーションのインストール

このアプリケーションと利用可能な他の ISV アプリケーションをインストールする詳細な手順については、[サードパーティ Hadoop アプリケーションのインストール](hdinsight-apps-install-applications.md)に関するページを参照してください。

## <a name="launch-dataiku-dss"></a>Dataiku DSS の起動

1. インストール後、Azure Portal でクラスターから DSS を起動できます。**[設定]** ウィンドウに移動し、**[全般]** カテゴリにある **[アプリケーション]** をクリックします。 [インストール済みアプリ] ウィンドウには、インストール済みのアプリケーションが一覧表示されます。

    ![インストールされた Dataiku DSS アプリ](./media/hdinsight-apps-install-dataiku/app.png)

2. HDInsight で DSS を選択したら、Web ページへのリンクと SSH エンドポイント パスが表示されます。 [Web ページ] リンクを選択します。

3. 初回起動時、新しい Dataiku アカウントを無料で作成するためのフォームが表示されます。または、既存のアカウントにサインインするためのフォームが表示されます。 さらに、[Enterprise Edition](https://www.dataiku.com/dss/editions/) の 2 週間の無料試用版を開始するオプションもあります。 この時点で、引き続き Enterprise Edition のライセンス キーを入力するか、または Community Edition を使用するオプションがあります。

4. 選択したライセンス オプションを完了した後、ログイン フォームが表示されます。 前に表示された既定の資格情報をログイン フォームに入力します。

次の手順では、簡単なデモを行います。

1. [サンプルの注文 CSV をダウンロードします](https://doc.dataiku.com/tutorials/data/101/haiku_shirt_sales.csv)。

2. DSS ダッシュボードから、左側にあるメニューの **+** (新しいプロジェクト) リンクを選択して、新しいプロジェクトを作成します。

    ![新しいプロジェクト リンク](./media/hdinsight-apps-install-dataiku/new-project.png)

3. [New project]\(新しいプロジェクト\) フォームで、**[Name]\(名前\)** を入力します。 **[Project Key]\(プロジェクト キー\)** には、推奨値が自動的に入力されます。 ここでは、「Orders」と入力します。 **[作成]** をクリックします。

    ![[New project]\(新しいプロジェクト\) フォーム](./media/hdinsight-apps-install-dataiku/new-project-form.png)

4. 新しいプロジェクトのページで **[+ IMPORT YOUR FIRST DATASET]\(+ 最初のデータセットをインポートする\)** を選択します。

    ![ファイルのアップロード](./media/hdinsight-apps-install-dataiku/import-dataset.png)

5. **[Files]\(ファイル\)** データセット リストで、**[Upload your files]\(ファイルのアップロード\)** を選択します。 アップロード ダイアログが表示されます。 [Add a file]\(ファイルの追加\) をクリックしてダウンロードした `haiku_shirt_sales.csv` ファイルを選択し、検証します。

6. ファイルが DSS にアップロードされます。 [Preview]\(プレビュー\) ボタンをクリックして、DSS によって CSV 形式が正しく検出されることを確認してください。

    ![ファイルのアップロード](./media/hdinsight-apps-install-dataiku/preview.png)

7. インポートはほぼ完了です。 この CSV ファイルではタブ区切りが使用されています。 データは、機能と呼ばれる列と観察を表す行を備えた表形式で表示されます。 一見してファイルのヘッダーとデータの間に空白の行があることがわかります。これが唯一のエラーです。 このエラーを解決するために、**[Skip next lines]\(次の行をスキップ\)** フィールドに「`1`」を入力します。

    ![保存](./media/hdinsight-apps-install-dataiku/skip-lines.png)

8. 新しいデータセットに名前を付けます。 画面の上部にあるフィールドに「**haiku_shirt_sales**」と入力し、**[Create]\(作成\)** ボタンを選択します。

9. 表形式ビューのデータが表示され、そこで探索を開始できます。 列ごとに、Dataiku Science Studio によって検出されたデータ型が "_青_" で表示されます。この場合、テキスト、数値、または日付 (未解析) です。 値が型に一致しない (赤) または欠けている (空白) と考えられる列の比率をゲージが示します。 この例のデータセットでは、部門に空の値と無効なデータの両方があります。

    ![表形式ビュー](./media/hdinsight-apps-install-dataiku/viewing-dataset.png)

## <a name="data-manipulation"></a>データ操作

実際のデータはほぼ常に乱雑であり、わかりやすくまとめられていることはほとんどありません。 通常、データの整理には一連のスクリプトと関連するビジネス ロジックが必要です。 Dataiku DSS には、ユーザーがこのタスクを簡単に行える専用の**ラボ** ツールが用意されています。

1. 右上隅にある **[Lab]\(ラボ\)** をクリックします。

    ![[Lab]\(ラボ\) ボタン](./media/hdinsight-apps-install-dataiku/lab-button.png)

2. ラボ ウィンドウが開きます。 ラボは、繰り返しデータセットを操作して掘り下げる場所です。 このチュートリアルでは、視覚的な分析についてデモを行います。 視覚的な分析の下にある **[New]\(新規\)** ボタンを選択します。 分析の名前を指定するよう求められます。 ここでは既定の名前のままにし、**[CREATE]\(作成\)** をクリックします。

    ![ラボの作成](./media/hdinsight-apps-install-dataiku/create-lab.png)

3. ページの右上隅にある **[Quick columns stats]\(列の簡易統計\)** ボタンを選択します。

    ![列の簡易統計](./media/hdinsight-apps-install-dataiku/quick-column-stats.png)

4. **[Columns quick view]\(列の簡易ビュー\)** ウィンドウに、タイムラインベースのグラフでデータ型と値の統計が表示されます。

    ![列の簡易ビュー](./media/hdinsight-apps-install-dataiku/columns-quick-view.png)

これで、サンプル データを使用して DSS を探索できます。 データを整理して操作し、新しい視覚化を作成できます。

詳細なチュートリアルについては、「[Learn Dataiku DSS (Dataiku DSS を学ぶ)](https://www.dataiku.com/learn/)」を参照してください。

## <a name="next-steps"></a>次の手順

* [Dataiku DSS のドキュメント](https://doc.dataiku.com/dss/latest/)。
* [カスタム HDInsight アプリケーションをインストールする](hdinsight-apps-install-custom-applications.md): 未発行の HDInsight アプリケーションを HDInsight にデプロイする方法について確認します。
* [HDInsight アプリケーションを発行する](hdinsight-apps-publish-applications.md): カスタム HDInsight アプリケーションを Azure Marketplace に発行する方法について確認します。
* [MSDN: HDInsight アプリケーションをインストールする](https://msdn.microsoft.com/library/mt706515.aspx): HDInsight アプリケーションを定義する方法について確認します。
* [スクリプト アクションを使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md): スクリプト アクションを使用してアプリケーションを追加インストールする方法を確認します。
* [HDInsight で空のエッジ ノードを使用する](hdinsight-apps-use-edge-node.md): HDInsight クラスターへのアクセスのほか、HDInsight アプリケーションのテストとホストに空のエッジ ノードを使用する方法を確認します。
