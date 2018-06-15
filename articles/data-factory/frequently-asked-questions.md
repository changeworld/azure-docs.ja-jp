---
title: 'Azure Data Factory: よく寄せられる質問 | Microsoft Docs'
description: Azure Data Factory についてよく寄せられる質問とその回答を紹介します。
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 532dec5a-7261-4770-8f54-bfe527918058
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: shlo
ms.openlocfilehash: b7f654fb3b24954bb1c9222a4e97dc4b6844411c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34619033"
---
# <a name="azure-data-factory-faq"></a>Azure Data Factory FAQ
この記事は、Azure Data Factory サービスのバージョン 2 に適用されます。 Data Factory についてよく寄せられる質問とその回答を紹介します。  

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[Data Factory バージョン 1 に関してよく寄せられる質問](v1/data-factory-faq.md)を参照してください。

## <a name="what-is-azure-data-factory"></a>Azure Data Factory とは何ですか。 
Data Factory は、データの移動や変換を自動化する完全管理型のクラウドベースのデータ統合サービスです。 原材料を機械で加工して最終製品を作成する工場と同じように、Azure Data Factory は生データを収集してすぐに使用できる情報に変換する既存のサービスを調整します。 

Azure Data Factory を使用すると、データ駆動型のワークフローを作成して、オンプレミスとクラウドのデータ ストア間でデータを移動できます。 さらに、Azure HDInsight、Azure Data Lake Analytics、SQL Server Integration Services (SSIS) 統合ランタイムなどのコンピューティング サービスを使用して、データを処理および変換できます。 

Data Factory を使用すると、Azure ベースのクラウド サービスか、SSIS、SQL Server、Oracle などの独自のセルフホステッド コンピューティング環境でデータ処理を実行できます。 必要なアクションを実行するパイプラインを作成した後で、それを定期的に実行するようにスケジュールすることができます (毎時、毎日、毎週など)。また、イベントの発生によってパイプラインをトリガーすることもできます。 詳細については、[Azure Data Factory の概要](introduction.md)に関するページをご覧ください。

## <a name="whats-different-in-version-2"></a>バージョン 2 の変更点
Azure Data Factory バージョン 2 は、元の Azure Data Factory のデータ移動と変換サービスを基に構築されていますが、対応できるクラウドファースト データ統合シナリオの幅が広がりました。 Azure Data Factory バージョン 2 では、次の機能が提供されます。

- フローとスケールの制御
- Azure での SSIS パッケージのデプロイと実行

バージョン 1 のリリース以降、お客様はクラウド、オンプレミス、クラウド仮想マシン (VM) 内でデータ移動と処理の両方を実行する、複雑なハイブリッド データ統合シナリオを設計することを必要としていることがわかりました。 これらの要件を満たすには、セキュリティで保護された仮想ネットワーク環境内でデータの転送と処理を行うと共に、オンデマンドの処理能力を使用してスケールアウトする必要があります。

データ パイプラインがビジネス分析戦略のより重要な要素になっているため、これらの活動には、データの増分読み込みやイベント トリガー型の実行をサポートする柔軟なスケジューリング機能が必要であることがわかりました。 複雑さが増すにつれて、分岐処理、ループ処理、条件付き処理などの一般的なワークフロー パラダイムをサポートするサービスもいっそう必要になります。

バージョン 2 では、既存の SSIS パッケージをクラウドに移行することもできます。 このアクションは、SSIS を、Data Factory 内で管理され、統合ランタイムの新機能を利用する Azure サービスとしてリフトアンドシフトします。 バージョン 2 で SSIS 統合ランタイムをスピンアップすることで、クラウド内で SSIS パッケージを実行、管理、監視、およびビルドすることができます。

### <a name="control-flows-and-scale"></a>フローとスケールの制御 
最新のデータ ウェアハウスの多様な統合フローとパターンをサポートするため、Data Factory では、時系列データと関連付けられない、新しい柔軟なデータ パイプライン モデルを利用できます。 このリリースでは、データ パイプラインの制御フロー内の条件や分岐をモデル化し、これらのフロー内またはフロー間でパラメーターを明示的に渡すことができます。

データ統合に必要な任意のフロー スタイルをモデル化でき、それをオンデマンドで、またはスケジュールどおりに繰り返し送信することができます。 これまで実現できなかったもので、今回実現できるようになった一般的なフローのいくつかを次に紹介します。   

- 制御フロー:
    - パイプライン内のシーケンスでのアクティビティの連鎖。
    - パイプライン内のアクティビティの分岐。
    - parameters
        - パイプライン レベルでパラメーターを定義し、パイプラインをオンデマンドで起動するときまたはトリガーから起動するときに引数を渡します。
        - アクティビティは、パイプラインに渡される引数を使用できます。
    - カスタム状態の受け渡し
        - 状態などのアクティビティ出力は、パイプライン内の後続のアクティビティで使用できます。
    - ループ コンテナー
        - For-each 
- トリガー ベースのフロー:
    - パイプラインは、オンデマンドで、または実時間に合わせてトリガーできます。
- 差分フロー:
    - 差分コピー用にパラメーターを使用したり最大値を定義したりして、オンプレミスやクラウドのリレーショナル ストアからディメンションまたは参照テーブルを移動し、データをレイクに読み込みます。 

詳細については、[制御フローのチュートリアル](tutorial-control-flow.md)に関するページを参照してください。

### <a name="deploy-ssis-packages-to-azure"></a>SSIS パッケージを Azure にデプロイする 
SSIS ワークロードを移動する場合は、Data Factory バージョン 2 を作成し、Azure-SSIS 統合ランタイムをプロビジョニングできます。 Azure-SSIS 統合ランタイムは、クラウドでの SSIS パッケージの実行専用の、Azure VM (ノード) の完全に管理されたクラスターです。 詳しい手順については、「[SSIS パッケージを Azure にデプロイする](tutorial-create-azure-ssis-runtime-portal.md)」チュートリアルを参照してください。 
 

### <a name="sdks"></a>SDK
プログラマティック インターフェイスを必要とする詳しい知識のあるユーザー向けに、バージョン 2 では、使い慣れた IDE を使用してパイプラインを作成、管理、監視するために使用できる豊富な SDK のセットが用意されています。

- **.NET SDK**: .NET SDK はバージョン 2 で更新されています。 
- **PowerShell**: PowerShell コマンドレットはバージョン 2 で更新されています。 バージョン 2 のコマンドレットには、名前に *DataFactoryV2* が含まれています。 たとえば、*Get-AzureRmDataFactoryV2* となります。 
- **Python SDK**: この SDK は、バージョン 2 で新しく導入されました。
- **REST API**: REST API はバージョン 2 で更新されています。  

バージョン 2 で更新された SDK は、バージョン 1 クライアントと下位互換性がありません。 

### <a name="monitoring"></a>監視
現在、バージョン 2 は SDK のみを使用したデータ ファクトリの監視をサポートしています。 ポータルは、まだバージョン 2 データ ファクトリの監視をサポートしていません。 

## <a name="what-is-integration-runtime"></a>統合ランタイムの概要
統合ランタイムは、さまざまなネットワーク環境間で以下のデータ統合機能を提供するために Azure Data Factory によって使用されるコンピューティング インフラストラクチャです。

- **データ移動**: パブリック ネットワーク内のデータ ストアとプライベート ネットワーク (オンプレミスまたは仮想プライベート ネットワーク) 内のデータ ストアの間でデータを移動します。 組み込みコネクタ、形式の変換、列のマッピング、パフォーマンスとスケーラビリティに優れたデータ転送に関するサポートを提供します。
- **アクティビティのディスパッチ**: Azure HDInsight、Azure Machine Learning、Azure SQL Database、SQL Server などのさまざまなコンピューティング サービスで実行される変換アクティビティをディスパッチして監視します。
- **SSIS パッケージの実行**: 管理されている Azure コンピューティング環境で SSIS パッケージをネイティブに実行します。

データの移動と変換に必要な 1 つ以上の統合ランタイム インスタンスをデプロイできます。 統合ランタイムは、Azure パブリック ネットワークまたはプライベート ネットワーク (オンプレミス、Azure Virtual Network、またはアマゾン ウェブ サービス Virtual Private Cloud (VPC)) で実行できます。 

詳細については、「[Azure Data Factory の統合ランタイム](concepts-integration-runtime.md)」を参照してください。

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>Integration Runtime 数の制限
1 つのデータ ファクトリに含めることができる統合ランタイム インスタンスの数に厳密な制限はありません。 ただし、SSIS パッケージの実行について、1 サブスクリプションにつき統合ランタイムが使用できる VM コア数には制限があります。 詳細については、「[Data Factory の制限](../azure-subscription-service-limits.md#data-factory-limits)」を参照してください。

## <a name="when-should-i-use-version-2-rather-than-version-1"></a>バージョン 1 ではなくバージョン 2 を使用する場合 
Azure Data Factory を初めて使用する場合は、直接バージョン 2 から始めてください。 既にバージョン 1 を使用している場合は、バージョン 2 でデータ ファクトリを再構築してください。

> [!WARNING]
> Data Factory のバージョン 2 は、一般公開 (GA) ではなくプレビュー段階です。 そのため、GA である Data Factory のバージョン 1 と同じ Azure サービス レベル アグリーメント (SLA) の義務の対象にはなりません。 

## <a name="what-are-the-top-level-concepts-of-version-2"></a>バージョン 2 の最上位の概念
1 つの Azure サブスクリプションで 1 つ以上の Azure Data Factory インスタンス (データ ファクトリ) を利用できます。 Azure Data Factory には、プラットフォームとして連携する 4 つの主要コンポーネントが含まれます。このプラットフォームを基盤とし、データ移動とデータ変換のステップを含んだデータ主導型のワークフローを作成することができます。

### <a name="pipelines"></a>パイプライン
データ ファクトリは、1 つまたは複数のパイプラインを持つことができます。 パイプラインは、1 つの作業単位を実行するための複数のアクティビティから成る論理的なグループです。 パイプライン内のアクティビティがまとまって 1 つのタスクを実行します。 たとえば、Azure BLOB からデータを取り込み、HDInsight クラスターで Hive クエリを実行してデータを分割するアクティビティのグループをパイプラインに含めることができます。 パイプラインには、アクティビティをセットとして管理でき、個別に管理せずに済むというメリットがあります。 パイプライン内のアクティビティは、連鎖して順次処理することも、独立して並行処理することもできます。

### <a name="activity"></a>アクティビティ
アクティビティは、パイプライン内の処理ステップを表します。 たとえば、"*コピー*" アクティビティを使用して、データ ストア間でデータをコピーできます。 同様に、Azure HDInsight クラスターに対して Hive クエリを実行する Hive アクティビティを使用して、データを変換または分析できます。 Data Factory では、データ移動アクティビティ、データ変換アクティビティ、制御アクティビティの 3 種類のアクティビティがサポートされています。

### <a name="data-sets"></a>データ セット
データ セットは、アクティビティ内でデータを入力または出力として使用するデータを単にポイントまたは参照する、データ ストア内のデータ構造を表します。 

### <a name="linked-services"></a>リンクされたサービス
リンクされたサービスは、接続文字列によく似ており、Data Factory が外部リソースに接続するために必要な接続情報を定義します。 リンクされたサービスはデータ ソースへの接続を定義するもので、データ セットはデータの構造を表すもの、と捉えることができます。 たとえば、Azure Storage のリンクされたサービスでは、Azure Storage アカウントに接続するための接続文字列を指定します。 また、Azure BLOB データ セットでは、データを格納する BLOB コンテナーやフォルダーを指定します。

Data Factory では、リンクされたサービスは 2 つの目的に使用されます。

- オンプレミスの SQL Server インスタンス、Oracle データベース インスタンス、ファイル共有、Azure Blob Storage アカウント、その他の "*データ ストア*" を表すため。 サポートされるデータ ストアの一覧については、「[Azure Data Factory のコピー アクティビティ](copy-activity-overview.md)」を参照してください。
- アクティビティの実行をホストできる *コンピューティング リソース* を表すため。 たとえば、HDInsight Hive アクティビティは HDInsight Hadoop クラスターで実行されます。 変換アクティビティとサポートされているコンピューティング環境の一覧については、「[Azure Data Factory でデータを変換する](transform-data.md)」を参照してください。

### <a name="triggers"></a>トリガー
トリガーは、パイプラインの実行をいつ開始するかを決定する処理単位を表します。 さまざまな種類のイベントに合わせて、さまざまな種類のトリガーがあります。 プレビュー用には、実時間のスケジューラ トリガーがサポートされています。 


### <a name="pipeline-runs"></a>パイプライン実行
パイプライン実行は、パイプラインを実行するインスタンスです。 通常は、パイプラインで定義されたパラメーターに引数を渡すことで、パイプライン実行をインスタンス化します。 引数は、手動で渡すことも、トリガー定義内で渡すこともできます。

### <a name="parameters"></a>parameters
パラメーターは、読み取り専用構成のキーと値のペアです。 パラメーターはパイプラインで定義し、定義されたパラメーターの引数を実行時に実行コンテキストから渡します。 実行コンテキストは、トリガーによって、または手動で実行するパイプラインから作成されます。 パイプライン内のアクティビティは、パラメーターの値を使用します。

データ セットとは、厳密に型指定されたパラメーターと、再利用または参照可能なエンティティのことです。 アクティビティは、データ セットを参照でき、データ セットの定義で定義されたプロパティを使用できます。

リンクされたサービスも厳密に型指定されたパラメーターであり、これにはデータ ストアかコンピューティング環境への接続情報が入ります。 これも、再利用または参照可能なエンティティです。

### <a name="control-flows"></a>制御フロー
制御フローは、パイプライン アクティビティを調整します。これには、シーケンス内のアクティビティの連鎖、分岐、パイプライン レベルで定義するパラメーター、オンデマンドかトリガーからパイプラインを起動した際に渡す引数が含まれます。 さらに、カスタム状態の受け渡しや、ループ コンテナー (つまり for-each 反復子) も制御フローに含まれます。


Data Factory の概念について詳しくは、次の記事をご覧ください。

- [データ セットとリンクされたサービス](concepts-datasets-linked-services.md)
- [パイプラインとアクティビティ](concepts-pipelines-activities.md)
- [Integration Runtime](concepts-integration-runtime.md)

## <a name="what-is-the-pricing-model-for-data-factory"></a>Data Factory の価格モデル
Azure Data Factory の価格の詳細については、[Data Factory の価格の詳細](https://azure.microsoft.com/pricing/details/data-factory/)に関するページを参照してください。

## <a name="what-regions-support-azure-data-factory-version-2"></a>Azure Data Factory バージョン 2 をサポートするリージョン
現時点でバージョン 2 のデータ ファクトリを作成できるリージョンは、米国東部、米国東部 2、西ヨーロッパです。 ただし、データ ファクトリで別リージョンの統合ランタイムを使用して、データ ストア間のデータの移動、コンピューティング サービスに対するアクティビティのディスパッチ、または SSIS パッケージのディスパッチを行うことができます。 詳細については、[Data Factory の場所](concepts-integration-runtime.md#integration-runtime-location)に関するセクションを参照してください。

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Data Factory の最新情報を入手する方法
Azure Data Factory の最新情報を入手するには、次のサイトを参照してください。

- [ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [ドキュメントのホーム ページ](/azure/data-factory)
- [製品のホーム ページ](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>技術的な詳細情報 

### <a name="can-version-1-and-version-2-pipelines-run-side-by-side"></a>バージョン 1 とバージョン 2 のパイプラインを並行して実行することはできますか?
いいえ。 バージョン 2 およびバージョン 1 のデータ ファクトリに他のバージョンのエンティティ (リンクされているサービス、データ セット、またはパイプライン) を含めることはできません。   

### <a name="do-i-still-need-to-define-data-sets-in-version-2"></a>バージョン 2 でもデータ セットを定義する必要はありますか?
ほとんどのアクティビティで、データ セットは必須のエンティティではなくなりました。 コピー、機械学習、参照、検証、および変換のためにデータ セット内のスキーマや他のメタデータ情報を使用するカスタム アクティビティには必要です。 その他のアクティビティでは、データ セットは必須ではなくなりました。

### <a name="can-i-chain-two-activities-without-a-data-set-in-version-2"></a>バージョン 2 ではデータ セットを使用せずに 2 つのアクティビティを連結できますか?
はい。 バージョン 2 では、データ セットを使用せずにアクティビティを連結できます。 アクティビティを連結するには、パイプラインの JSON 定義で **dependsOn** プロパティを使用します。 

### <a name="are-all-the-version-1-activities-supported-in-version-2"></a>バージョン 1 のすべてのアクティビティはバージョン 2 でサポートされていますか? 
はい。バージョン 1 のすべてのアクティビティがバージョン 2 でサポートされています。

### <a name="how-can-i-schedule-a-version-2-pipeline"></a>バージョン 2 のパイプラインをスケジュールするにはどうすればよいですか? 
バージョン 2 のパイプラインをスケジュールするには、スケジューラのトリガーを使用できます。 トリガーでは、実時間カレンダー スケジュールが使用されます。これを使用して、定期的に、またはカレンダーベースの周期パターン (たとえば、毎週月曜日午後 6 時と木曜日午後 9 時など) を使用してパイプラインをスケジュールできます。 詳細については、[パイプラインの実行とトリガー](concepts-pipeline-execution-triggers.md)に関するページを参照してください。

### <a name="can-i-pass-parameters-to-a-pipeline-run-in-version-2"></a>バージョン 2 で実行されるパイプラインにパラメーターを渡すことはできますか?
はい。パラメーターは、バージョン 2 においてファースト クラスの最上位の概念です。 パイプライン レベルでパラメーターを定義し、パイプライン実行をオンデマンドで実行するときまたはトリガーを使用して実行するときに、引数を渡すことができます。  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>パイプライン パラメーターの既定値は定義できますか? 
はい。 パイプラインではパラメーターの既定値を定義できます。 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>パイプラインのアクティビティはパイプライン実行に渡される引数を使用できますか? 
はい。 パイプライン内の各アクティビティは、パイプラインに渡されて `@parameter` コンストラクトで実行されるパラメーター値を使用できます。 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>アクティビティの出力プロパティは別のアクティビティで使用できますか? 
はい。 アクティビティの出力は、`@activity` コンストラクトを使用して後続のアクティビティで使用できます。
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>アクティビティの出力で null 値を適切に処理するにはどうすればよいですか? 
式で `@coalesce` コンストラクトを使用すると、null 値を適切に処理できます。 

### <a name="can-i-use-retry-and-timeout-at-the-activity-level-in-version-2"></a>バージョン 2 では、アクティビティ レベルで再試行とタイムアウトを使用できますか?
はい。 バージョン 2 でバージョン 1 と同様にアクティビティの実行を制御するには、アクティビティ レベルで再試行とタイムアウトを構成します。 

## <a name="next-steps"></a>次の手順
バージョン 2 のデータ ファクトリを作成する手順については、次のチュートリアルを参照してください。

- [クイックスタート: データ ファクトリを作成する](quickstart-create-data-factory-dot-net.md)
- [チュートリアル: クラウドのデータをコピーする](tutorial-copy-data-dot-net.md)

