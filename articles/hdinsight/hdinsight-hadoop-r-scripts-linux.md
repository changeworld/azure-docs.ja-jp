---
title: "Linux ベースの HDInsight での GNU R のインストール | Microsoft Docs"
description: "R をインストールして使用し、Linux ベースの Hadoop クラスターをカスタマイズする方法について説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 7b758492-87bf-4d82-8b8c-1664e7d177bd
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cfaade8249a643b77f3d7fdf466eb5ba38143f18
ms.openlocfilehash: 9dd06f04344c1ea8e4c1511c8ea05b7c17b3329b
ms.lasthandoff: 03/01/2017

---
# <a name="install-and-use-gnu-r-on-linux-based-hdinsight-hadoop-clusters"></a>Linux ベースの HDInsight Hadoop クラスターに GNU R をインストールして使用する

**Script Action** クラスターのカスタマイズを使用して HDInsight 上の Hadoop で任意の種類のクラスターに GNU R をインストールできます。 データ サイエンティストおよびアナリストは、R を使用することにより、HDInsight にデプロイされる Hadoop クラスターで大量のデータを処理することができます。

> [!WARNING]
> このドキュメントでは、HDInsight に GNU R をカスタム アクションとしてインストールする手順を示します。 これは、__HDInsight での Microsoft R Server__ と同じ機能を提供__しません__。 このドキュメントの手順ではなく、HDInsight で R Server を使用することをお勧めします。
>
> HDInsight で R Server を使用する方法については、「[HDInsight での R Server の概要](hdinsight-hadoop-r-server-get-started.md)」を参照してください。 

## <a name="what-is-r"></a>R とは

<a href="http://www.r-project.org/" target="_blank">統計計算用 R プロジェクト</a>は、統計計算用のオープン ソースの言語と環境です。 R は、数百の組み込み統計関数と、関数型プログラミングとオブジェクト指向のプログラミングの特徴を結合した独自のプログラミング言語を提供します。 また、広範なグラフィカル機能も提供します。 R は、さまざまな分野におけるほぼすべての統計学専門家や科学者に推奨されるプログラミング環境です。

作成時に Script Action を使用してカスタマイズされた HDInsight の Hadoop クラスター上で R スクリプトを実行し、R 環境をインストールできます。 R は Azure BLOB ストレージ (WASB) と互換性があるので、そこに格納されているデータは HDInsight 上の R を使用して処理できます。

## <a name="what-the-script-does"></a>スクリプトの機能

HDInsight クラスターへの R のインストールに使用するスクリプト アクションでは、次の Ubuntu パッケージがインストールされ、基本的な R インストールが提供されます。

* [r-base](http://packages.ubuntu.com/precise/r-base): GNU R の基本パッケージ
* [r-base-dev](http://packages.ubuntu.com/precise/r-base-dev): GNU R の補助パッケージ

次の RHadoop パッケージもインストールされ、MapReduce および HDFS との統合が実現します。

* [rmr2](https://github.com/RevolutionAnalytics/rmr2): R 開発者が Hadoop MapReduce を使用できるようになります。
* [rhdfs](https://github.com/RevolutionAnalytics/rhdfs): R 開発者が Hadoop HDFS (HDInsight 用 WASB) を使用できるようになります。

さらに、次の R パッケージがインストールされます。

| R パッケージ | 提供内容 |
| --- | --- |
| [rJava](https://cran.r-project.org/web/packages/rJava/index.html) |Java インターフェイスへの低レベル R |
| [Rcpp](https://cran.r-project.org/web/packages/Rcpp/index.html) |R と C++ の統合 |
| [RJSONIO](https://cran.r-project.org/web/packages/RJSONIO/index.html) |R オブジェクトと JSON の間のシリアル化/逆シリアル化 |
| [bitops](https://cran.r-project.org/web/packages/bitops/index.html) |整数ベクトルのビット演算機能。 |
| [digest](https://cran.r-project.org/web/packages/digest/index.html) |R オブジェクトの暗号化ハッシュ ダイジェストの作成 |
| [functional](https://cran.r-project.org/web/packages/functional/index.html) |Curry、Compose などの高階関数 |
| [reshape2](https://cran.r-project.org/web/packages/reshape2/index.html) |データの柔軟な再構築と集計 |
| [stringr](https://cran.r-project.org/web/packages/stringr/index.html) |一般的な文字列操作用の単純かつ整合性のあるラッパー |
| [plyr](https://cran.r-project.org/web/packages/plyr/index.html) |データの分割、適用、組み合わせのためのツール |
| [caTools](https://cran.r-project.org/web/packages/caTools/index.html) |移動ウィンドウ統計、GIF、Base64、ROC AUC などのためのツール |
| [stringdist](https://cran.r-project.org/web/packages/stringdist/index.html) |近似文字列マッチング関数および文字列距離関数 |

## <a name="install-r-using-script-actions"></a>スクリプト アクションを使用した R のインストール

次のスクリプト アクションを使用して、R を HDInsight クラスターにインストールできます。 https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh

このセクションでは、Azure Portal を使用してクラスターを作成する際にこのスクリプトを使用する方法について説明します。 

> [!NOTE]
> スクリプト アクションは、Azure PowerShell、Azure CLI、HDInsight .NET SDK、または Azure Resource Manager のテンプレートを使用して適用することもできます。 既に実行しているクラスターにスクリプト アクションを適用することもできます。 詳細については、 [スクリプト アクションを使用した HDInsight クラスターのカスタマイズ](hdinsight-hadoop-customize-cluster-linux.md)に関する記事を参照してください。

1. [Linux ベースの HDInsight クラスターのプロビジョニング](hdinsight-hadoop-provision-linux-clusters.md)に関するページに記載されている手順を使用して、クラスターのプロビジョニングを開始します。ただし、プロビジョニングを完了しないでください。

2. **[オプションの構成]** ブレードで **[スクリプト アクション]** を選択し、以下の情報を使用してフォームを完成させます。
   
   * **[名前]**: スクリプト アクションの表示名を入力します。
   * **SCRIPT URI**: https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh
   * **[ヘッド]**: このオプションをオンにします。
   * **[ワーカー]**: このオプションをオンにします。
   * **[Zookeeper]**: Zookeeper ノードにインストールするには、このオプションをオンにします。
   * **[パラメーター]**: このフィールドは空のままにします。

3. **[スクリプト アクション]** の下部で、**[選択]** を使用して構成を保存します。 最後に、**[オプションの構成]** ブレードの下部にある **[選択]** を使用して、オプションの構成情報を保存します。

4. 「[Provision Linux-based HDInsight clusters (Linux ベースの HDInsight クラスターのプロビジョニング)](hdinsight-hadoop-provision-linux-clusters.md)」の説明に従って、クラスターのプロビジョニングを続行します。

## <a name="run-r-scripts"></a>R スクリプトの実行

クラスターがプロビジョニングを完了したら、次の手順に従い、R を使用してクラスター上で MapReduce 操作を実行します。

1. SSH を使用して HDInsight クラスターに接続します。
   
        ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net
   
    HDInsight での SSH の使用方法の詳細については、次のドキュメントを参照してください。
   
   * [Windows 10、Linux、Unix、または OS X の Bash から HDInsight (Hadoop) で SSH を使用する](hdinsight-hadoop-linux-use-ssh-unix.md)
   * [HDInsight の Linux ベースの Hadoop で Windows から SSH (PuTTY) を使用する](hdinsight-hadoop-linux-use-ssh-windows.md)

2. `username@hn0-CLUSTERNAME:~$` プロンプトで次のコマンドを入力し、R の対話型セッションを開始します。
   
        R

3. 次の R プログラムを入力します。 このコードでは 1 ～ 100 の数値を生成してから 2 倍します。
   
        library(rmr2)
        ints = to.dfs(1:100)
        calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))
   
    最初の行では、RHadoop ライブラリの rmr2 を呼び出しています。これは、MapReduce 操作で使用します。
   
    2 行目では、1 ～ 100 の値を生成し、`to.dfs` を使用して Hadoop ファイル システムに格納しています。
   
    3 行目では、rmr2 で提供される機能を使用して MapReduce プロセスを作成し、処理を開始しています。 処理が始まると、複数の行がスクロール表示されます。

4. 次に、以下のコードを使用して、MapReduce 出力の格納先になっていた一時パスを確認します。
   
        print(calc())
   
    このコマンドでは `/tmp/file5f615d870ad2` のような値が返されます。 実際の出力を表示するには、次のコマンドを使用します。
   
        print(from.dfs(calc))
   
    次のテキストは出力例を示しています。
   
        [1,]  1 2
        [2,]  2 4
        .
        .
        .
        [98,]  98 196
        [99,]  99 198
        [100,] 100 200

5. R を終了するには、次のコマンドを入力します。
   
        q()

## <a name="next-steps"></a>次のステップ

* [HDInsight クラスターでの Giraph のインストール](hdinsight-hadoop-giraph-install.md) クラスターのカスタマイズを使用して、HDInsight Hadoop クラスターに Giraph をインストールします。 Giraph は、Hadoop でグラフの処理を実行するために使用でき、Azure HDInsight で使用できます。
* [HDInsight クラスターに Solr をインストールする](hdinsight-hadoop-solr-install.md)。 クラスターのカスタマイズを使用して、HDInsight Hadoop クラスターに Solr をインストールします。 Solr は、格納されたデータに対して強力な検索操作を実行することができます。
* [HDInsight クラスターに Hue をインストールする](hdinsight-hadoop-hue-linux.md)。 クラスターのカスタマイズを使用して、HDInsight Hadoop クラスターに Hue をインストールします。 Hue は Hadoop クラスターとの情報のやりとりに使用される一連の Web アプリケーションです。

[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md

