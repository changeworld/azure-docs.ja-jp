---
title: Azure HDInsight での R Server の概要 | Microsoft Docs
description: HDInsight 上の R Server を使用してビッグ データ分析用のアプリケーションを作成する方法について説明します。
services: hdinsight
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.assetid: 6dc21bf5-4429-435f-a0fb-eea856e0ea96
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/23/2018
ms.author: nitinme
ms.openlocfilehash: 19334e78124d1e388bc760659385388d89953644
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2018
---
# <a name="introduction-to-r-server-and-open-source-r-capabilities-on-hdinsight"></a>HDInsight での R Server とオープン ソース R の機能の概要

Azure で HDInsight クラスターを作成するときのデプロイ オプションとして Microsoft R Server (別称: Microsoft Machine Learning Server) を選択できるようになりました。 このオプションを提供するクラスターの種類は、**Microsoft R Server** と呼ばれます。 この機能により、データ サイエンティスト、統計学者、R プログラマは、HDInsight でのスケーラブルで分散型の分析手法にオンデマンドでアクセスできるようになります。

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

HDInsight の R Server は、Azure Blob Storage または Data Lake Storage に読み込まれた事実上すべてのサイズのデータセットで R ベースの分析を行うための最新の機能を備えています。 Microsoft R Server クラスターはオープン ソース R を基盤としているため、自ら構築する R ベースのアプリケーションで 8,000 を超えるオープン ソース R パッケージを活用できます。 また、ScaleR (R Server に付属する Microsoft のビッグ データ分析パッケージ) のルーチンも使用できます。

クラスターのエッジ ノードは、クラスターへの接続と R スクリプトの実行に便利な場所です。 エッジ ノードでは、エッジ ノード サーバーのコア間で、ScaleR の並列化された分散関数を実行できます。 また、ScaleR の Hadoop Map Reduce または Spark コンピューティング コンテキストを使用して、クラスターのノード間でこれらの関数を実行することもできます。

分析によって得られたモデルや予測は、ダウンロードしてオンプレミスで使用できます。 また、[Azure Machine Learning Studio](http://studio.azureml.net) などの [Web サービス](../../machine-learning/studio/publish-a-machine-learning-web-service.md)を使用して、Azure の別の場所で運用化することもできます。

## <a name="get-started-with-r-server-on-hdinsight"></a>HDInsight での R Server の使用

Azure HDInsight に Microsoft R Server クラスターを作成するには、Azure Portal を使って HDInsight クラスターを作成するときに、**Microsoft R Server** クラスター タイプを選びます。 この R Server クラスター タイプは、クラスターのデータ ノード上の R Server と、R Server ベースの分析用のランディング ゾーンとしてのエッジ ノードを組み込みます。 クラスターの作成手順については、[HDInsight での R Server の使用](r-server-get-started.md)に関する記事をご覧ください。

## <a name="why-choose-r-server-in-hdinsight"></a>HDInsight で Microsoft R Server を選ぶ理由

HDInsight の Microsoft R Server は、Azure で Microsoft R Server を使うオプションを提供します。 Microsoft R Server には次のものが含まれます。

+ **Microsoft およびオープン ソースからの最高の AI 革新**

  Microsoft は、すべての個人と組織が AI にアクセスできるように努力しています。 Microsoft R Server には、物理メモリより大きいサイズのデータを処理でき、分散方式のさまざまなプラットフォームで実行できる、[RevoscaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler)、[revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package)、[microsoftML](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package) などの、拡張性が高い分散アルゴリズムの豊富なセットが含まれています。 製品に付属する Microsoft のカスタム [R パッケージ](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference)と [Python パッケージ](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference)のコレクションの詳細をご覧ください。
  
  Microsoft R Server は、単一のエンタープライズ レベルのプラットフォームで、Microsoft の新技術とオープン ソース コミュニティの技術 (R、Python、AI ツールキット) を統合します。 R または Python のすべてのオープン ソース機械学習パッケージが、Microsoft 独自の革新技術とサイド バイ サイドで動作できます。 

+ **簡単で、安全性が高く、高スケールの運用化と管理**

  従来の運用化パラダイムと環境に依存する企業は、この部分に多くの時間と労力を費やすことになります。 これらの問題により、モデルの変換時間、有効で最新に維持するためのイテレーション、規制の承認、運用化によるアクセス許可の管理などのコストと遅延が増えることがよくあります。

  Microsoft R Server は、クラス最高の[運用化](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)を提供します。機械学習モデルが完成した時点から、数回クリックするだけで Web サービス API が生成されます。 これらの [Web サービス](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services)はオンプレミスまたはクラウドのサーバー グリッドでホストされ、基幹業務アプリケーションと統合できます。 弾力性のあるグリッドに展開できるので、バッチとリアルタイム スコアリングの両方について、ビジネスのニーズに合わせてシームレスに拡張できます。 方法については、「[HDInsight の Microsoft R Server の運用化](r-server-operationalize.md)」をご覧ください。

+ **最適な総保有コストでユーザーに成功をもたらすエコシステムとの深い結び付き**

  アプリケーションをインテリジェントにしたり、単に AI と機械学習の新しい世界を学習したりしようとするときは、開始時に役立つ適切なリソースが必要です。 このドキュメント以外に、Microsoft は、複数の学習用リソースを提供し、いくつかのトレーニング パートナーと協力して短時間で能力を高めて生産を始められるように支援しています。


## <a name="key-features-of-r-server-on-hdinsight"></a>HDInsight での Microsoft R Server の主要な機能

HDInsight の Microsoft R Server には次の機能が含まれています。

| 機能カテゴリ | [説明] |
|------------------|-------------|
| R 対応 | [R パッケージ](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference): R で記述されたソリューション用であり、スクリプト実行用の R とランタイムのインフラストラクチャのオープン ソース ディストリビューションを含みます。 |
| Python 対応 | [Python モジュール](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference): Python で記述されたソリューション用であり、スクリプト実行用の Python とランタイムのインフラストラクチャのオープン ソース ディストリビューションを含みます。  
| [事前トレーニング済みモデル](https://docs.microsoft.com/machine-learning-server/install/microsoftml-install-pretrained-models) | ビジュアル分析とテキストのセンチメント分析に対応し、ユーザーが提供するデータをスコア付けできる状態になっています。 |
| [展開と使用](r-server-operationalize.md) | サーバーを運用化し、Web サービスとしてソリューションを展開します。 |
| [リモート実行](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-server-or-client) | クライアント ワークステーションからネットワーク上の Microsoft R Server でリモート セッションを開始します。 |


## <a name="data-storage-options-for-r-server-on-hdinsight"></a>HDInsight の Microsoft R Server 向けのデータ ストレージ オプション

HDInsight クラスターの HDFS ファイル システムの既定のストレージを、Azure ストレージ アカウントまたは Azure Data Lake Store と関連付けることができます。 この関連付けにより、分析中にクラスター ストレージにアップロードされるデータがどのようなものであっても確実に永続化され、クラスターが削除された後であってもデータを利用できます。 選択したストレージ オプションへのデータ転送を担うさまざまなツールが用意されています。これには、ストレージ アカウントのポータル ベースのアップロード機能や [AzCopy](../../storage/common/storage-use-azcopy.md) ユーティリティなどがあります。

選択したプライマリ ストレージにかかわらず、クラスター プロビジョニング プロセス中に追加の BLOB および Data Lake Store へのアクセスを追加することができます。 アカウント アクセスの追加については、[HDInsight での R Server の使用](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-get-started)に関する記事をご覧ください。 複数のストレージ アカウントの使用について詳しくは、補足記事「[HDInsight の R Server 向けの Azure Storage オプション](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-storage)」をご覧ください。

エッジ ノードで使用するストレージ オプションとして、 [Azure Files](../../storage/files/storage-how-to-use-files-linux.md) を使用することもできます。 Azure Files では、Azure Storage で作成されたファイル共有を Linux ファイル システムにマウントできます。 HDInsight クラスターの R Server を対象としたこれらのデータ ストレージ オプションについて詳しくは、「[HDInsight の Microsoft R Server 向けの Azure Storage オプション](r-server-storage.md)」をご覧ください。

## <a name="access-machine-learning-server-on-the-cluster"></a>クラスター上の Machine Learning Server にアクセスする

ブラウザーを使用して、エッジ ノードの Microsoft Machine Learning Server に接続することができます。 これは、クラスターの作成時に既定でインストールされます。 詳しくは、「[HDInsight での R Server の概要](r-server-get-started.md)」をご覧ください。 ML Server には、コマンドラインから SSH/PuTTY を使用して R コンソールにアクセスすることによって接続することもできます。 

## <a name="develop-and-run-r-scripts"></a>R スクリプトの開発と実行

ユーザーが作成して実行する R スクリプトでは、ScaleR ライブラリの並列化された分散ルーチンに加え、8,000 以上のオープン ソース R パッケージを使用できます。 一般に、エッジ ノードの R Server で実行されるスクリプトは、そのノードの R インタープリター内で実行されます。 コンピューティング コンテキストが Hadoop Map Reduce (RxHadoopMR) または Spark (RxSpark) に設定された ScaleR 関数を呼び出す必要のあるステップは例外です。 この場合、関数は参照先データに関連付けられたクラスターのデータ (タスク) ノード間に分散した形で実行されます。 各種コンピューティング コンテキスト オプションの詳細については、「[HDInsight の R Server の計算コンテキストのオプション](r-server-compute-contexts.md)」を参照してください。

## <a name="operationalize-a-model"></a>モデルの運用化

データ モデリングが完了したら、モデルを運用化して、Azure とオンプレミスの両方で新しいデータについての予測を行うことができます。 このプロセスはスコア付けと呼ばれます。 スコア付けは、HDInsight や Azure Machine Learning のほか、オンプレミスで行うことができます。

### <a name="score-in-hdinsight"></a>HDInsight でのスコア付け
HDInsight でスコア付けを行うには、モデルを呼び出して、ストレージ アカウントに読み込んだ新しいデータ ファイルについて予測を行う R 関数を作成します。 その後、予測をストレージ アカウントに保存します。 このルーチンは、クラスターのエッジ ノードでオンデマンドで実行することも、スケジュールされたジョブを使用して実行することもできます。

### <a name="score-in-azure-machine-learning-aml"></a>Azure Machine Learning (AML) でのスコア付け
Azure Machine Learning を使ってスコア付けを行うには、[AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html) と呼ばれるオープン ソースの Azure Machine Learning R パッケージを使って、モデルを Azure Web サービスとして発行します。 便宜上、このパッケージはエッジ ノードにあらかじめインストールされています。 次に、Azure Machine Learning の機能を使って Web サービスのユーザー インターフェイスを作成し、スコア付けが必要なときに Web サービスを呼び出します。

この方法を選んだ場合、ScaleR モデル オブジェクトを、Web サービスで使う同等のオープン ソース モデル オブジェクトに変換する必要があります。 この変換には、ScaleR 強制型変換関数 (アンサンブルベースのモデルの `as.randomForest()` など) を使用できます。

### <a name="score-on-premises"></a>オンプレミスのスコア付け
モデルの作成後にオンプレミスのスコア付けを行うには、R でモデルをシリアル化し、モデルをダウンロードして逆シリアル化してから、新しいデータのスコア付けに使用します。 新しいデータのスコア付けは、「[HDInsight でのスコア付け](#scoring-in-hdinsight)」で前述した方法を使用するか、[DeployR](https://deployr.revolutionanalytics.com/) を使用して実行できます。

## <a name="maintain-the-cluster"></a>クラスターの管理

### <a name="install-and-maintain-r-packages"></a>R パッケージのインストールと管理

使用する R パッケージのほとんどは、R スクリプトのほとんどの手順が実行されるエッジ ノードに配置する必要があります。 エッジ ノードに追加の R パッケージをインストールするには、R の通常の `install.packages()` メソッドを使用します。

クラスターで ScaleR ライブラリのルーチンを使用するだけであれば、通常はデータ ノードに追加の R パッケージをインストールする必要はありません。 ただし、データ ノードで **rxExec** または **RxDataStep** 実行の使用をサポートするために、追加のパッケージが必要になる場合があります。

このような場合、クラスターの作成後にスクリプト アクションを使って追加のパッケージをインストールできます。 詳しくは、「[Azure HDInsight で R Server クラスターを管理する](r-server-hdinsight-manage.md)」をご覧ください。

### <a name="change-hadoop-mapreduce-memory-settings"></a>Hadoop MapReduce のメモリ設定の変更

クラスターに変更を加えることで、MapReduce ジョブの実行時に Microsoft R Server で使用可能なメモリ容量を変更できます。 クラスターを変更するには、クラスターの Azure ポータル ブレードで使用できる Apache Ambari UI を使用します。 クラスターの Ambari UI にアクセスする手順については、「 [Ambari Web UI を使用した HDInsight クラスターの管理](../hdinsight-hadoop-manage-ambari.md)」をご覧ください。

次のように、**RxHadoopMR** の呼び出しで Hadoop スイッチを使って、Microsoft R Server で使用可能なメモリ容量を変更することもできます。

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>クラスターのスケーリング

HDInsight 上の既存の Microsoft R Server クラスターは、ポータルを使ってスケールアップまたはスケールダウンできます。 スケールアップにより、大規模な処理タスクに必要な追加容量が得られます。また、クラスターがアイドル状態のときに、クラスターを縮小することもできます。 クラスターのスケーリングの手順については、[HDInsight クラスターの管理](../hdinsight-administer-use-portal-linux.md)に関する記事をご覧ください。

### <a name="maintain-the-system"></a>システムのメンテナンス

OS 修正プログラムや他の更新プログラムを適用するために、HDInsight クラスターの基になる Linux VM で時間外にメンテナンスが実施されます。 通常、メンテナンスは毎週月曜日と木曜日の午前 3 時 30 分 (VM のローカル時刻に基づく) に実施されます。 更新は、4 分の 1 以上のクラスターに同時に影響を与えないような方法で実行されます。  

ヘッド ノードが冗長化されており、すべてのデータ ノードが影響を受けるわけではないため、この期間に実行中のジョブは速度が低下する場合がありますが、 ただし、完了するまで引き続き実行する必要があります。 クラスターの再構築を必要とする致命的な障害が発生しない限り、カスタム ソフトウェアやローカル データはメンテナンス イベントで保持されます。

## <a name="ide-options-for-r-server-on-an-hdinsight-cluster"></a>HDInsight クラスターの Microsoft R Server の IDE オプション

HDInsight クラスターの Linux エッジ ノードは、R ベースの分析のランディング ゾーンです。 HDInsight の最新バージョンには、ブラウザー ベースの IDE としてエッジ ノードへの RStudio Server の既定のインストールが備わっています。 R スクリプトの開発と実行に IDE として RStudio Server を使用すると、単に R コンソールを使用した場合よりも生産性を大幅に高めることができます。

さらに、デスクトップ IDE をインストールし、リモートの MapReduce または Spark コンピューティング コンテキストを使ってクラスターにアクセスできます。 そのようなオプションとしては、Microsoft の [R Tools for Visual Studio](https://www.visualstudio.com/features/rtvs-vs.aspx) (RTVS)、RStudio、Walware の Eclipse ベースの [StatET](http://www.walware.de/goto/statet) が挙げられます。

また、SSH または PuTTY 経由で接続した後、単に Linux コマンド プロンプトで「**R**」と入力して、エッジ ノードの R コンソールにアクセスすることもできます。 コンソール インターフェイスを使用しているときは、別のウィンドウで R スクリプト開発用のテキスト エディターを実行し、必要に応じてスクリプトのセクションを切り取って R コンソールに貼り付けると利便性が向上します。

## <a name="pricing"></a>価格

Microsoft R Server を含む HDInsight クラスターに関する価格は、Standard HDInsight クラスターの価格と同様に体系化されています。 料金は、ネーム、データ、エッジの各ノードの基になる VM のサイズに基づきますが、コア時間の割増分が加算されます。 HDInsight の価格の詳細については、「 [HDInsight 価格](https://azure.microsoft.com/pricing/details/hdinsight/)」をご覧ください。

## <a name="next-steps"></a>次の手順

HDInsight クラスターで Microsoft R Server を使用する方法の詳細については、次のトピックを参照してください。

* [HDInsight での R Server クラスターの使用](r-server-get-started.md)
* [HDInsight の R Server クラスター向けのコンピューティング コンテキスト オプション](r-server-compute-contexts.md)
* [HDInsight の R Server クラスター向けの Azure Storage オプション](r-server-storage.md)
