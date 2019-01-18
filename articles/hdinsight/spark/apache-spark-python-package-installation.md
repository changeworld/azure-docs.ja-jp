---
title: スクリプト アクション - Azure HDInsight の Jupyter で Python パッケージをインストールする
description: スクリプト アクションを使用して HDInsight の Spark clusters で Jupyter notebooks を構成し、外部の Python パッケージを使用する方法に関する詳細な手順。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: f804cfd693a37099edc22e7f4861d6d7e1af0fc7
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/20/2018
ms.locfileid: "53651115"
---
# <a name="use-script-action-to-install-external-python-packages-for-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>スクリプト アクションを使用して HDInsight の Apache Spark クラスターの Jupyter Notebook で外部の Python パッケージをインストールする
> [!div class="op_single_selector"]
> * [cell magic の使用](apache-spark-jupyter-notebook-use-external-packages.md)
> * [スクリプト アクションの使用](apache-spark-python-package-installation.md)

スクリプト アクションを使用して、HDInsight (Linux) 上の [Apache Spark](https://spark.apache.org/) クラスターを、そのクラスターの標準では搭載されていない外部のコミュニティから提供されている **python** パッケージを使用するよう構成する方法について説明します。

> [!NOTE]  
> `%%configure` マジックを使用して Jupyter Notebook を構成して外部パッケージを使用することもできます。 手順については、「[HDInsight の Apache Spark クラスターの Jupyter Notebook で外部のパッケージを使用する](apache-spark-jupyter-notebook-use-external-packages.md)」を参照してください。

[パッケージ インデックス](https://pypi.python.org/pypi)で、利用できるすべてのパッケージを検索できます。 公開されているパッケージの一覧を他のソースから入手してもかまいません。 たとえば、[conda-forge](https://conda-forge.org/feedstocks/) で使用できるパッケージをインストールできます。

この記事では、例として、クラスターでスクリプト アクションを使用して [TensorFlow](https://www.tensorflow.org/) パッケージをインストールし、Jupyter Notebook で使用する方法について説明します。

## <a name="prerequisites"></a>前提条件
次のものが必要です。

* Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
* HDInsight での Apache Spark クラスター。 手順については、「 [Create Apache Spark clusters in Azure HDInsight (Azure HDInsight での Apache Spark クラスターの作成)](apache-spark-jupyter-spark-sql.md)」を参照してください。

   > [!NOTE]  
   > HDInsight Linux に Spark クラスターがない場合は、クラスターの作成時にスクリプト アクションを実行できます。 [カスタム スクリプト アクションの使用方法](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)に関するドキュメントを参照してください。
   
## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>HDInsight クラスターで使用するオープン ソース ソフトウェアのサポート

Microsoft Azure HDInsight サービスは Apache Hadoop を中心に形成されたオープン ソース テクノロジのエコシステムを利用します。 Microsoft Azure は、オープン ソース テクノロジの一般的なレベルのサポートを提供します。 詳細については、「[Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」の**サポート範囲**に関するセクションを参照してください。 HDInsight サービスでは、組み込みのコンポーネントに対してさらに高いレベルのサポートを提供しています。

HDInsight サービスで利用できるオープン ソース コンポーネントには、2 つの種類があります。

* **組み込みコンポーネント** - これらのコンポーネントは、HDInsight クラスターにプレインストールされており、クラスターの主要な機能を提供します。 たとえば、Apache Hadoop YARN リソース マネージャー、 Apache Hive クエリ言語 (HiveQL)、Mahout ライブラリなどがこのカテゴリに属します。 クラスター コンポーネントの完全な一覧は、「[HDInsight で提供されるApache Hadoop クラスター バージョンの新機能](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning)」から入手できます。
* **カスタム コンポーネント** - クラスターのユーザーは、コミュニティで入手できるコンポーネントや自作のコンポーネントを、インストールするか、ワークロード内で使用することができます。

> [!WARNING]   
> HDInsight クラスターに用意されているコンポーネントは全面的にサポートされており、 これらのコンポーネントに関連する問題の分離と解決については、Microsoft サポートが支援します。
>
> カスタム コンポーネントについては、問題のトラブルシューティングを進めるための支援として、商業的に妥当な範囲のサポートを受けることができます。 Microsoft サポートによって問題が解決する場合もあれば、オープン ソース テクノロジに関する深い専門知識を入手できる場所への参加をお願いする場合もあります。 たとえば、[HDInsight についての MSDN フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)や [https://stackoverflow.com](https://stackoverflow.com) などの数多くのコミュニティ サイトを利用できます。 また、Apache プロジェクトには、[https://apache.org](https://apache.org) に[Hadoop](https://hadoop.apache.org/) などのプロジェクト サイトもあります。


## <a name="use-external-packages-with-jupyter-notebooks"></a>Jupyter Notebook で外部のパッケージを使用する

1. [Azure Portal](https://portal.azure.com/) のスタート画面で Spark クラスターのタイルをクリックします (スタート画面にピン留めしている場合)。 **[すべて参照]** > **[HDInsight クラスター]** でクラスターに移動することもできます。   

2. Spark クラスター ブレードで、左側のウィンドウの **[スクリプト アクション]** をクリックします。 スクリプトの種類 [カスタム] を使用し、スクリプト アクションのフレンドリ名を入力します。 **ヘッド ノードとワーカー ノード**でスクリプトを実行し、パラメーター フィールドを空のままにします。 bash スクリプトは、次の場所から参照できます。 https://hdiconfigactions.blob.core.windows.net/linuxtensorflow/tensorflowinstall.sh [カスタム スクリプト アクションの使用方法](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)に関するドキュメントを参照してください。

   > [!NOTE]  
   > クラスターには 2 つの Python インストールがあります。 Spark では、`/usr/bin/anaconda/bin` の Anaconda Python インストールが使用され、これは既定で Python 2.7 環境になります。 Python 3.x を使用し、PySpark3 カーネルにパッケージをインストールするには、その環境用の `conda` 実行可能ファイルへのパスを使用し、`-n` パラメーターを使用して、環境を指定します。 たとえば、コマンド `/usr/bin/anaconda/envs/py35/bin/conda install -c conda-forge ggplot -n py35` は、`conda-forge` チャネルを使用して `ggplot` パッケージを Python 3.5 環境にインストールします。

3. PySpark Jupyter Notebook を開きます

    ![新しい Jupyter Notebook の作成](./media/apache-spark-python-package-installation/hdinsight-spark-create-notebook.png "新しい Jupyter Notebook の作成")

4. Untitled.pynb という名前の新しい Notebook が作成されて開かれます。 上部の Notebook 名をクリックし、わかりやすい名前を入力します。

    ![Notebook の名前を指定](./media/apache-spark-python-package-installation/hdinsight-spark-name-notebook.png "Notebook の名前を指定")

5. 次に `import tensorflow` によって、hello world の例を実行します。 

    コピーするコード:

        import tensorflow as tf
        hello = tf.constant('Hello, TensorFlow!')
        sess = tf.Session()
        print(sess.run(hello))

    結果は次のようになります。
    
    ![TensorFlow コード実行](./media/apache-spark-python-package-installation/execution.png "TensorFlow コードの実行")

## <a name="seealso"></a>関連項目
* [概要:Azure HDInsight での Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>シナリオ
* [Apache Spark と BI:HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](apache-spark-use-bi-tools.md)
* [Apache Spark と Machine Learning:HDInsight で Spark を使用して、HVAC データを使用して建物の温度を分析する](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark と Machine Learning:HDInsight で Spark を使用して食品の検査結果を予測する](apache-spark-machine-learning-mllib-ipython.md)
* [HDInsight 上での Apache Spark を使用した Web サイト ログ分析](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>アプリケーションの作成と実行
* [Scala を使用してスタンドアロン アプリケーションを作成する](apache-spark-create-standalone-application.md)
* [Apache Livy を使用して Apache Spark クラスターでジョブをリモートから実行する](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>ツールと拡張機能
* [HDInsight の Apache Spark クラスターの Jupyter Notebook で外部のパッケージを使用する](apache-spark-jupyter-notebook-use-external-packages.md)
* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Spark Scala アプリケーションを作成し、送信する](apache-spark-intellij-tool-plugin.md)
* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Apache Spark アプリケーションをリモートでデバッグする](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight 上の Apache Spark クラスターで Apache Zeppelin Notebook を使用する](apache-spark-zeppelin-notebook.md)
* [HDInsight 用の Apache Spark クラスター内の Jupyter Notebook で使用可能なカーネル](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>リソースの管理
* [Azure HDInsight での Apache Spark クラスターのリソースの管理](apache-spark-resource-manager.md)
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](apache-spark-job-debugging.md)
