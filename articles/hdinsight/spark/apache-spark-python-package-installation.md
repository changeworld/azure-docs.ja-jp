---
title: Azure HDInsight の Jupyter を使用した Python パッケージに対するスクリプト アクション
description: スクリプト アクションを使用して HDInsight の Spark clusters で Jupyter notebooks を構成し、外部の Python パッケージを使用する方法に関する詳細な手順。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: a8654f6c9c6c6d020872d2c89e0dd141db4e0451
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2019
ms.locfileid: "74215613"
---
# <a name="safely-manage-python-environment-on-azure-hdinsight-using-script-action"></a>スクリプト アクションを使用して Azure HDInsight で Python 環境を安全に管理する

> [!div class="op_single_selector"]
> * [cell magic の使用](apache-spark-jupyter-notebook-use-external-packages.md)
> * [スクリプト アクションの使用](apache-spark-python-package-installation.md)

HDInsight では、Spark クラスターに 2 つの Python のインストール (Anaconda Python 2.7 と Python 3.5) が組み込まれています。 場合によっては、ユーザーが外部の Python パッケージや別の Python のバージョンのインストールなどを行って、Python 環境をカスタマイズする必要があります。 この記事では、HDInsight 上の [Apache Spark](https://spark.apache.org/) クラスターで Python 環境を安全に管理するためのベスト プラクティスを示します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。

* HDInsight での Apache Spark クラスター。 手順については、「 [Create Apache Spark clusters in Azure HDInsight (Azure HDInsight での Apache Spark クラスターの作成)](apache-spark-jupyter-spark-sql.md)」を参照してください。

   > [!NOTE]  
   > HDInsight Linux に Spark クラスターがない場合は、クラスターの作成時にスクリプト アクションを実行できます。 [カスタム スクリプト アクションの使用方法](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)に関するドキュメントを参照してください。

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>HDInsight クラスターで使用するオープン ソース ソフトウェアのサポート

Microsoft Azure HDInsight サービスは Apache Hadoop を中心に形成されたオープン ソース テクノロジのエコシステムを利用します。 Microsoft Azure は、オープン ソース テクノロジの一般的なレベルのサポートを提供します。 詳細については、[Azure サポート FAQ Web サイト](https://azure.microsoft.com/support/faq/)を参照してください。 HDInsight サービスでは、組み込みのコンポーネントに対してさらに高いレベルのサポートを提供しています。

HDInsight サービスで利用できるオープン ソース コンポーネントには、2 つの種類があります。

* **組み込みコンポーネント** - これらのコンポーネントは、HDInsight クラスターにプレインストールされており、クラスターの主要な機能を提供します。 たとえば、Apache Hadoop YARN リソース マネージャー、Apache Hive クエリ言語 (HiveQL)、Mahout ライブラリがこのカテゴリに属します。 クラスター コンポーネントの完全な一覧は、「[HDInsight で提供されるApache Hadoop クラスター バージョンの新機能](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning)」から入手できます。
* **カスタム コンポーネント** - クラスターのユーザーは、コミュニティで入手できるコンポーネントや自作のコンポーネントを、インストールするか、ワークロード内で使用することができます。

> [!IMPORTANT]
> HDInsight クラスターに用意されているコンポーネントは全面的にサポートされており、 これらのコンポーネントに関連する問題の分離と解決については、Microsoft サポートが支援します。
>
> カスタム コンポーネントについては、問題のトラブルシューティングを進めるための支援として、商業的に妥当な範囲のサポートを受けることができます。 Microsoft サポートによって問題が解決する場合もあれば、オープン ソース テクノロジに関する深い専門知識を入手できる場所への参加をお願いする場合もあります。 たとえば、次のような数多くのコミュニティ サイトを利用できます: [HDInsight についての MSDN フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)や [https://stackoverflow.com](https://stackoverflow.com) などの数多くのコミュニティ サイトを利用できます。 また、Apache プロジェクトには、[https://apache.org](https://apache.org) に[Hadoop](https://hadoop.apache.org/) などのプロジェクト サイトもあります。

## <a name="understand-default-python-installation"></a>Python の既定のインストールを理解する

HDInsight Spark クラスターは、Anaconda のインストール付きで作成されます。 クラスターには 2 つの Python インストールが存在します (Anaconda Python 2.7 と Python 3.5)。 次の表に、Spark、Livy、および Jupyter 向けの既定の Python 設定を示します。

| |Python 2.7|Python 3.5|
|----|----|----|
|Path|/usr/bin/anaconda/bin|/usr/bin/anaconda/envs/py35/bin|
|Spark|既定で 2.7 に設定|該当なし|
|Livy|既定で 2.7 に設定|該当なし|
|Jupyter|PySpark カーネル|PySpark3 カーネル|

## <a name="safely-install-external-python-packages"></a>外部 Python パッケージを安全にインストールする

HDInsight クラスターは、組み込みの Python 環境 (Python 2.7 と Python 3.5 の両方) に依存しています。 これらの既定の組み込み環境にカスタム パッケージを直接インストールすると、予期しないライブラリ バージョンの変更が発生し、クラスターがさらに壊れる可能性があります。 Spark アプリケーションのカスタム外部 Python パッケージを安全にインストールするには、次の手順に従います。

1. conda を使用して Python 仮想環境を作成します。 仮想環境によって、他を壊すことなく、プロジェクト用の分離された空間が用意されます。 Python 仮想環境を作成するときに、使用する Python のバージョンを指定できます。 Python 2.7 と 3.5 を使用する場合でも、仮想環境を作成する必要があることに注意してください。 これは、クラスターの既定の環境を壊さないようにするためです。 次のスクリプトを使用して、Python 仮想環境を作成するクラスターのすべてのノードで、スクリプト アクションを実行します。 

    -   `--prefix` には、conda 仮想環境を作成するパスを指定します。 ここで指定したパスに基づいて、さらに変更する必要がある構成がいくつかあります。 この例では、クラスターに py35 という名前の既存の仮想環境が既に存在するため、py35new が使用されています。
    -   `python=` には、仮想環境用の Python のバージョンを指定します。 この例では、バージョン3.5 が使用されています。これは、クラスターに組み込まれているものと同じバージョンです。 Python の他のバージョンを使用して仮想環境を作成することもできます。
    -   `anaconda` は、package_spec を anaconda として指定して、仮想環境に Anaconda パッケージをインストールします。
    
    ```bash
    sudo /usr/bin/anaconda/bin/conda create --prefix /usr/bin/anaconda/envs/py35new python=3.5 anaconda --yes 
    ```

2. 必要に応じて、作成した仮想環境に外部の Python パッケージをインストールします。 次のスクリプトを使用して、クラスターのすべてのノードでスクリプト アクションを実行して、外部の Python パッケージをインストールします。 仮想環境フォルダーにファイルを書き込むために、ここでは sudo 特権が必要です。

    [パッケージ インデックス](https://pypi.python.org/pypi)で、利用できるすべてのパッケージを検索できます。 公開されているパッケージの一覧を他のソースから入手してもかまいません。 たとえば、[conda-forge](https://conda-forge.org/feedstocks/) で使用できるパッケージをインストールできます。

    -   `seaborn` は、インストールするパッケージの名前です。
    -   `-n py35new` には、先ほど作成した仮想環境の名前を指定します。 仮想環境の作成に応じて、名前を適宜変更してください。

    ```bash
    sudo /usr/bin/anaconda/bin/conda install seaborn -n py35new --yes
    ```

    仮想環境名がわからない場合は、クラスターのヘッダー ノードに SSH 接続し、`/usr/bin/anaconda/bin/conda info -e` を実行して、すべての仮想環境を表示できます。

3. Spark と Livy の構成を変更して、作成した仮想環境をポイントします。

    1. Ambari UI を開き、[Spark2] ページの [Configs]\(構成\) タブに移動します。
    
        ![Ambari を使用して Spark と Livy の構成を変更する](./media/apache-spark-python-package-installation/ambari-spark-and-livy-config.png)
 
    2. [Advanced livy2] を展開し、下のステートメントを末尾に追加します。 仮想環境を別のプレフィックスを使用してインストールした場合は、パスを適宜変更します。

        ```
        export PYSPARK_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        export PYSPARK_DRIVER_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        ```

        ![Ambari を使用して Livy 構成を変更する](./media/apache-spark-python-package-installation/ambari-livy-config.png)

    3. [Advanced spark2-env] を展開し、末尾にある既存の export PYSPARK_PYTHON ステートメントを置き換えます。 仮想環境を別のプレフィックスを使用してインストールした場合は、パスを適宜変更します。

        ```
        export PYSPARK_PYTHON=${PYSPARK_PYTHON:-/usr/bin/anaconda/envs/py35new/bin/python}
        ```

        ![Ambari を通して Spark 構成を変更する](./media/apache-spark-python-package-installation/ambari-spark-config.png)

    4. 変更を保存し、影響を受けるサービスを再起動します。 これらの変更では、Spark2 サービスを再起動する必要があります。 Ambari UI によって、再起動が必要であることを示すリマインダーが表示されます。[再起動] をクリックして、影響を受けるすべてのサービスを再起動します。

        ![Ambari を通して Spark 構成を変更する](./media/apache-spark-python-package-installation/ambari-restart-services.png)
 
4.  新しく作成された仮想環境を Jupyter で使用する場合は、 Jupyter の構成を変更し、Jupyter を再起動する必要があります。 次のステートメントを使用して、すべてのヘッダー ノードでスクリプト アクションを実行して、新たに作成された仮想環境に対して Jupyter をポイントします。 パスを仮想環境用に指定したプレフィックスに必ず変更してください。 このスクリプト アクションを実行した後、Ambari UI から Jupyter サービスを再起動して、この変更を使用できるようにします。

    ```
    sudo sed -i '/python3_executable_path/c\ \"python3_executable_path\" : \"/usr/bin/anaconda/envs/py35new/bin/python3\"' /home/spark/.sparkmagic/config.json
    ```

    次のコードを実行して、Jupyter Notebook の Python 環境を再確認できます。

    ![Jupyter Notebook の Python のバージョンを確認する](./media/apache-spark-python-package-installation/check-python-version-in-jupyter.png)

## <a name="known-issue"></a>既知の問題

Anaconda のバージョン 4.7.11 と4.7.12 には既知のバグがあります。 スクリプト アクションが `"Collecting package metadata (repodata.json): ...working..."` でハングし、`"Python script has been killed due to timeout after waiting 3600 secs"` で失敗した場合は、 [このスクリプト](https://gregorysfixes.blob.core.windows.net/public/fix-conda.sh)をダウンロードし、すべてのノードでスクリプト アクションとして実行することで問題を解決できます。

Anaconda のバージョンを確認するには、クラスターのヘッダー ノードに SSH 接続し、`/usr/bin/anaconda/bin/conda --v` を実行します。

## <a name="seealso"></a>関連項目

* [概要: Azure HDInsight での Apache Spark](apache-spark-overview.md)

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
