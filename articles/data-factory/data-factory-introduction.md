---
title: "データ統合サービスである Data Factory の概要 | Microsoft Docs"
description: "データの移動と変換を調整、自動化するクラウド データ統合サービスである Azure Data Factory について説明します。"
keywords: "データ統合, クラウド データ統合, Azure Data Factory とは"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: cec68cb5-ca0d-473b-8ae8-35de949a009e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/21/2017
ms.author: shlo
translationtype: Human Translation
ms.sourcegitcommit: 260208e7c7a08110eb3c885ef86ec4c18ff42fc9
ms.openlocfilehash: 40552b5d3cea5b04826c08e7b4b1d046a9fcefba
ms.lasthandoff: 04/23/2017


---
# <a name="introduction-to-azure-data-factory-service-a-data-integration-service-in-the-cloud"></a>クラウドによるデータ統合サービスである Azure Data Factory サービスの概要
## <a name="what-is-azure-data-factory"></a>Azure Data Factory とは何ですか。
ビッグ データの世界では、既存のデータがビジネスにどのように活用されるのでしょうか。 オンプレミスのデータ ソースや他の各種データ ソースから取得した参照データを使用して、クラウドで生成されたデータを補強することはできるのでしょうか。 ビッグ データの世界に必要なのは、多種多様なソースのデータを集約して処理するためのプラットフォームです。 Azure Data Factory は、データの**移動**や**変換**を調整して自動化する、クラウドベースのデータ統合サービスです。 データ統合ソリューションを作成して、さまざまなデータ ストアから入力データを取り込み、データを変換/処理して結果データを他のデータ ストアに発行することができます。 

![Diagram: Data Factory Overview, a data integration service](./media/data-factory-introduction/what-is-azure-data-factory.png)

**図 1.** さまざまなデータ ソースからデータを取り込み、準備、変換、分析を行って、すぐ使用できるデータを発行します。


## <a name="what-does-it-offer"></a>どのような機能が提供されますか。 
従来のデータ統合プロジェクトは、抽出-変換-読み込み (ETL) 処理を中心として展開されてきました。ETL とは、次の図に示すように、組織内の各種データ ソースからデータを抽出し、エンタープライズ データ ウェアハウス (EDW) のターゲット スキーマに準拠するようにデータを変換して、EDW にデータを読み込む処理です。 この処理の後、EDW は、BI 分析ソリューションから単一のデータ ソースとしてアクセスされます。

![従来の ETL](media/data-factory-introduction/traditional-etl.png)
**従来の ETL**

今日の企業を取り巻くデータ環境は、次の図に示すように、量、種類、複雑さのいずれにおいても指数関数的に増大し続けています。 形式もスピードも異なるオンプレミス データとクラウドで生成されたデータが混ざり合い、かつてないほど多様化しています。 データ処理は、さまざまな場所で行わなければならず、オープン ソース ソフトウェア、商用ソリューション、カスタム処理サービスが併用され、高コストで、統合や保守が困難なものとなっています。 変化し続ける現代のビッグ データ環境への適用に欠かせない機敏性は、現代の情報生成システムとして求められる各種機能を従来の EDW に組み合わせることで獲得できます。 Azure Data Factory は、従来の EDW と変化するデータ環境の両方で機能する構成プラットフォームです。企業は利用可能なすべてのデータを活用してデータ主導の意志決定を行えるようになります。

![新しいビッグ データ環境](media/data-factory-introduction/new-big-data-landscape.png)
**新しいビッグ データ環境**

企業は Azure Data Factory サービスというプラットフォームで、**データの処理、保存、および移動のサービスを組み合わせて情報生成パイプラインを構成し**、信頼できるデータ資産を管理することで、このような多様性を活用できます。

Azure Data Factory サービスでは、以下のことを実行できます。
- **さまざまなデータ保存システムやデータ処理システムと簡単に連携**。 

    企業が所有するデータは多種多様で、ソースもさまざまです。 情報生成システム構築の最初のステップは、SaaS サービス、ファイル共有、FTP、Web サービスなど、必要なすべてのデータ ソースと処理の機能に接続し、必要に応じて、後続の処理を行う 1 か所にデータを移動させることです。

    Data Factory を使用していない企業では、カスタムのデータ移動コンポーネントを構築するか、これらのデータ ソースと処理を統合するカスタム サービスを作成しなければなりません。 このような作業にはコストがかかり、システムの統合や保守が容易ではありません。エンタープライズ クラスの監視やアラートが欠け、完全に管理されたサービスが提供できるような制御機能を用意できないことも少なくありません。

    Data Factory を使用すれば、データ パイプラインのコピー アクティビティで、オンプレミスとクラウドの両方のソース データ ストアからクラウド内の一元化されたデータ ストアにデータを移動し、詳しく分析することができます。 たとえば、Azure Data Lake Store でデータを収集し、後で Azure Data Lake Analytics コンピューティング サービスを使用してデータを変換することができます。 または、Azure BLOB ストレージでデータを収集し、後で Azure HDInsight Hadoop クラスターを使用してデータを変換することもできます。
- **データを信頼できる情報に変換**。 

    データがクラウド上の一元的なデータ ストアに集まったら、データ パイプラインを作成してデプロイする必要があります。これにより、保守しやすい管理されたスケジュールで、信頼性の高い方法でデータ変換を実行し、信頼できるデータを運用環境に提供できます。 Azure Data Factory のデータ変換は、Azure HDInsight Hadoop クラスター、Azure Machine Learning VM、または VM の Azure Batch プールで実行される Hive、Pig、MapReduce、Azure Machine Learning バッチ実行、およびカスタム C# アクティビティなどの変換アクティビティを通じて行われます。
- **1 か所でデータ パイプラインを監視**。

    多様なデータ ポートフォリオを使用する場合は、ストレージ、処理、データ移動のサービスについて、全体像を確実に把握することが重要です。 Data Factory を使用すると、データ パイプライン正常性をエンドツーエンドで迅速に評価し、問題を特定して、必要に応じて修正処置を行うことができます。 また、どのソースのデータでも、データ系列やデータ間の関係を視覚的に追跡できます。 ジョブ実行の課金履歴、システム正常性、依存関係に関するすべての情報を、1 つの監視ダッシュボードで把握できます。

## <a name="how-does-it-work"></a>それはどのように機能しますか? 
Azure Data Factory には 3 つの情報生成段階があります。

![3 つの情報生成段階](media/data-factory-introduction/three-information-production-stages.png)

- **接続と収集**: この段階では、さまざまなデータ ソースからデータが 1 か所に収集されます。
- **変換と強化**: この段階では、収集されたデータが処理または変換されます。
- **発行**:  この段階では、BI ツールや分析ツールなどのアプリケーションで使用できるように、データが発行されます。

## <a name="key-components"></a>主なコンポーネント
1 つの Azure サブスクリプションで 1 つ以上の Azure Data Factory インスタンス (データ ファクトリ) を利用できます。 Azure Data Factory は、4 つの主要コンポーネントで構成されたプラットフォームです。このプラットフォームを基盤として、既存のデータ フローに対するデータ移動とデータ変換のオーケストレーションをシンプルなものから複雑なものまで構成できます。

### <a name="activity"></a>アクティビティ
アクティビティは、データに対して実行するアクションを定義します。 たとえば、コピー アクティビティを使用して、データ ストア間でデータをコピーできます。 同様に、Azure HDInsight クラスターに対して Hive クエリを実行する Hive アクティビティを使用して、データを変換または分析できます。 Data Factory では、データ移動アクティビティとデータ変換アクティビティの 2 種類のアクティビティがサポートされています。

各アクティビティは 0 個以上の入力データセットを受け取り、1 個以上の出力データセットを生成できます。 


### <a name="data-movement-activities"></a>データ移動アクティビティ
Data Factory のコピー アクティビティは、ソース データ ストアからシンク データ ストアにデータをコピーします。 Data Factory は次のデータ ストアをサポートしています。 また、任意のソースのデータを任意のシンクに書き込むことができます。 データ ストアをクリックすると、そのストアとの間でデータをコピーする方法がわかります。

[!INCLUDE [data-factory-supported-data-stores](../../includes/data-factory-supported-data-stores.md)]

詳細については、[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事を参照してください。

### <a name="data-transformation-activities"></a>データ変換アクティビティ
[!INCLUDE [data-factory-transformation-activities](../../includes/data-factory-transformation-activities.md)]

詳細については、[データ変換アクティビティ](data-factory-data-transformation-activities.md)に関する記事を参照してください。

コピー アクティビティでサポートされていないデータ ストアとの間でデータを移動する必要がある場合、つまり独自のロジックを使用してデータを変換する場合は、 **カスタム .NET アクティビティ**を作成します。 カスタム アクティビティの作成と使用の詳細については、「 [Azure Data Factory パイプラインでカスタム アクティビティを使用する](data-factory-use-custom-activities.md)」をご覧ください。

### <a name="pipeline"></a>パイプライン
パイプラインは、アクティビティをグループ化したものです。 パイプライン内のアクティビティがまとまって 1 つのタスクを実行します。 たとえば、パイプラインに Azure BLOB からデータを取り込むアクティビティのグループを含め、HDInsight クラスターで Hive クエリを実行してログ データをパーティション分割することができます。 パイプラインのメリットは、アクティビティを個別にではなく、セットとして管理できることです。 たとえば、デプロイとスケジュール設定を、アクティビティごとではなく、パイプライン単位で行うことができます。

### <a name="datasets"></a>データセット
データセットは、データ ストア内のデータ構造を表しています。アクティビティ内でデータを入力または出力として使用したい場合は、そのデータをポイントまたは参照するだけで済みます。 たとえば、Azure BLOB データセットは、パイプラインによるデータの読み取り先として、Azure BLOB ストレージ内の BLOB コンテナーと BLOB フォルダーを指定します。 

### <a name="linked-services"></a>リンクされたサービス
リンクされたサービスは、接続文字列によく似ており、Data Factory が外部リソースに接続するために必要な接続情報を定義します。 データセットはデータの構造を表すもので、リンクされたサービスはデータ ソースへの接続を定義するもの、と捉えることができます。  Data Factory ではリンクされたサービスは 2 つの目的に使用されます。

* オンプレミスの SQL Server、Oracle データベース、ファイル共有、Azure Blob Storage アカウント、その他の **データ ストア** を表すため。 サポートされているデータ ストアの一覧については、 [データ移動アクティビティ](#data-movement-activities) に関する記事をご覧ください。
* アクティビティの実行をホストできる **コンピューティング リソース** を表すため。 たとえば、HDInsightHive アクティビティは HDInsight Hadoop クラスターで実行されます。 サポートされているコンピューティング環境の一覧については、[データ変換アクティビティ](#data-transformation-activities)に関する記事をご覧ください。

### <a name="relationship-between-data-factory-entities"></a>Data Factory エンティティ間の関係
![図: Data Factory、クラウド データ統合サービス - 主要な概念](./media/data-factory-introduction/data-integration-service-key-concepts.png)
**図 2.** データセット、アクティビティ、パイプライン、リンクされたサービスの間の関係

## <a name="supported-regions"></a>サポートされているリージョン
現時点では、データ ファクトリは、**米国西部**、**米国東部**、**北ヨーロッパ** リージョンで作成できます。 ただし、データ ファクトリは、他の Azure リージョン内のデータ ストアやコンピューティング サービスにアクセスし、データ ストア間でデータを移動したり、コンピューティング サービスを使用してデータを処理したりできます。

Azure Data Factory 自体は、データを保存しません。 Azure Data Factory を使用すると、データ主導型のフローを作成し、[サポートされているデータ ストア](data-factory-data-movement-activities.md#supported-data-stores-and-formats)間でのデータ移動と、他のリージョンまたはオンプレミス環境にある[コンピューティング サービス](data-factory-compute-linked-services.md)を使用したデータ処理を調整できます。 また、プログラムと UI の両方のメカニズムを使用して、 [ワークフローを監視および管理](data-factory-monitor-manage-pipelines.md) することもできます。

Data Factory を利用できるリージョンが**米国西部**、**米国東部**、**北ヨーロッパ**のみであっても、Data Factory 内でデータ移動を実行するサービスは、いくつかのリージョンで[グローバル](data-factory-data-movement-activities.md#global)に利用できます。 データ ストアがファイアウォールの内側にある場合は、オンプレミス環境にインストールされている [Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) がデータを移動します。

たとえば、Azure HDInsight クラスターや Azure Machine Learning などのコンピューティング環境が西ヨーロッパ リージョン以外で稼働しているものと想定します。 北ヨーロッパに Azure Data Factory インスタンスを作成して利用すると、西ヨーロッパのコンピューティング環境でジョブのスケジュール設定にそのインスタンスを使用することができます。 Data Factory がコンピューティング環境でジョブをトリガーするまでに数ミリ秒かかりますが、コンピューティング環境でのジョブの実行時間は変わりません。

将来的には、Azure がサポートするすべての地域で Azure Data Factory が提供される予定です。

## <a name="next-steps"></a>次のステップ
データ パイプラインでデータ ファクトリを構築する方法については、以下のチュートリアルで紹介されている具体的な手順に従ってください。

| チュートリアル | Description |
| --- | --- |
| [2 つのクラウド データ ストア間でのデータの移動](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) |このチュートリアルでは、BLOB ストレージから SQL データベースに **データを移動** するパイプラインを備えたデータ ファクトリを作成します。 |
| [Hadoop クラスターを使用したデータの変換](data-factory-build-your-first-pipeline.md) |このチュートリアルでは、Azure HDInsight (Hadoop) クラスターで Hive スクリプトを実行して **データを処理する** データ パイプラインを備えた最初の Azure データ ファクトリを構築します。 |
| [Data Management Gateway を使用したオンプレミス データ ストアとクラウド データ ストア間でのデータの移動](data-factory-move-data-between-onprem-and-cloud.md) |このチュートリアルでは、**オンプレミス**の SQL Server データベースから Azure BLOB に**データを移動**するパイプラインを備えたデータ ファクトリを構築します。 チュートリアルの一環として、ご使用のコンピューターに Data Management Gateway をインストールして構成します。 |

