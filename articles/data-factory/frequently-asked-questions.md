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
ms.date: 06/27/2018
ms.author: shlo
ms.openlocfilehash: ebe8745db06113d0508d86554bf031a4235c8e44
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045951"
---
# <a name="azure-data-factory-faq"></a>Azure Data Factory FAQ
この記事では、Azure Data Factory に関してよく寄せられる質問に対する回答を示します。  

## <a name="what-is-azure-data-factory"></a>Azure Data Factory とは何ですか。 
Data Factory は、データの移動や変換を自動化する完全管理型のクラウドベースのデータ統合サービスです。 原材料を機械で加工して最終製品を作成する工場と同じように、Azure Data Factory は生データを収集してすぐに使用できる情報に変換する既存のサービスを調整します。 

Azure Data Factory を使用すると、データ駆動型のワークフローを作成して、オンプレミスとクラウドのデータ ストア間でデータを移動できます。 さらに、Azure HDInsight、Azure Data Lake Analytics、SQL Server Integration Services (SSIS) 統合ランタイムなどのコンピューティング サービスを使用して、データを処理および変換できます。 

Data Factory を使用すると、Azure ベースのクラウド サービスか、SSIS、SQL Server、Oracle などの独自のセルフホステッド コンピューティング環境でデータ処理を実行できます。 必要なアクションを実行するパイプラインを作成した後で、それを毎時、毎日、毎週など、定期的に実行するようにスケジュールを設定できます (時間ウィンドウ スケジューリング)。また、イベントの発生によってパイプラインをトリガーすることもできます。 詳細については、[Azure Data Factory の概要](introduction.md)に関するページをご覧ください。

### <a name="control-flows-and-scale"></a>フローとスケールの制御 
最新のデータ ウェアハウスの多様な統合フローとパターンをサポートするように、Data Factory はフル コントロールのフロー プログラミング パラダイムを含む、柔軟性の高いデータ パイプライン モデリングを有効にします。これには条件付き実行、データ パイプラインでの分岐が含まれており、これらのフロー内およびフロー間でパラメーターを明示的に渡します。 また、制御フローは、コピー アクティビティを介した大規模なデータ移動など、外部の実行エンジンへのアクティビティのディスパッチやデータ フローの機能を通じたデータの変換を包含します。

Data Factory はデータ統合に必要な任意のフロー スタイルをモデル化し、それをオンデマンドで、またはスケジュールどおりに繰り返し送信することができます。 次の一般的なフローがこのモデルで有効になります。   

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

### <a name="transform-your-data-at-scale-with-code-free-pipelines"></a>コード不要のパイプラインで大規模なデータを変換する
この新しいブラウザーベースのツール エクスペリエンスは、最新のインタラクティブな Web ベースのエクスペリエンスにより、コード不要のパイプラインのオーサリングとデプロイを提供します。

ビジュアル データの開発者やデータ エンジニアにとっては、ADF Web UI がパイプラインのビルドに使用するコード不要のデザイン環境です。 Visual Studio Online Git と完全に統合されており、CI/CD およびデバッグ オプション付きの反復開発との統合を提供します。

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>詳しい知識のあるユーザー向けの豊富なクロス プラットフォーム SDK
プログラマティック インターフェイスを必要とする詳しい知識のあるユーザー向けに、ADF V2 では、使い慣れた IDE を使用してパイプラインを作成、管理、監視するために活用できる豊富な SDK のセットが用意されています。
1.  Python SDK
2.  Powershell CLI
3.  C# SDK ユーザーは、ドキュメントに記載の REST API を使用して、ADF V2 と連動させることもできます。

### <a name="iterative-development-and-debugging-using-visual-tools"></a>ビジュアル ツールを使用した反復開発とデバッグ
Azure Data Factory (ADF) ビジュアル ツールを使用すると、反復開発とデバッグを行うことができます。 パイプラインを作成し、パイプライン キャンバスのデバッグ機能を使用して、1 行もコードを記述せずにテストを実行できます。 テストの実行結果は、パイプライン キャンバスの [出力] ウィンドウに表示されます。 テストの実行が成功したら、パイプラインにさらにアクティビティを追加し、反復的な方法でデバッグを続行できます。 進行中のテストをキャンセルすることもできます。 [デバッグ] をクリックする前にデータ ファクトリ サービスの変更をパブリッシュする必要はありません。 これは、データ ファクトリのワークフローを更新する前に、新しい追加や変更が開発環境、テスト環境、運用環境で期待どおりに動作することを確認する場合に便利です。 

### <a name="deploy-ssis-packages-to-azure"></a>SSIS パッケージを Azure にデプロイする 
SSIS ワークロードを移動する場合は、データ ファクトリを作成し、Azure-SSIS 統合ランタイムをプロビジョニングできます。 Azure-SSIS 統合ランタイムは、クラウドでの SSIS パッケージの実行専用の、Azure VM (ノード) の完全に管理されたクラスターです。 詳しい手順については、「[SSIS パッケージを Azure にデプロイする](tutorial-create-azure-ssis-runtime-portal.md)」チュートリアルを参照してください。 
 
### <a name="sdks"></a>SDK
プログラマティック インターフェイスを必要とする詳しい知識のあるユーザー向けに、ADF では、使い慣れた IDE を使用してパイプラインを作成、管理、監視するために使用できる豊富な SDK のセットが用意されています。 .NET、PowerShell、Python、REST などの言語がサポートされています。

### <a name="monitoring"></a>監視
データ ファクトリは、PowerShell、SDK、またはブラウザー ユーザー インターフェイス内のビジュアル モニタリング ツールで監視できます。 オンデマンド、トリガー ベース、およびクロック駆動のカスタム フローで効果的かつ効率的に監視および管理できます。 既存タスクをキャンセルする、一目でエラーを確認する、ドリルダウンして詳細なエラー メッセージを取得する、問題をデバッグするなど、コンテキストを切り替えたり、画面を前後に移動したりすることなく、すべて 1 つのウィンドウで行うことができます。 

### <a name="new-features-for-ssis-in-adf"></a>ADF の SSIS の新機能
2017 年のパブリック プレビュー リリースより、Data Factory は SSIS に次の機能を追加しています。

-   プロジェクトやパッケージの SSIS カタログ (SSISDB) をホストする、Azure SQL Database (DB) の次の 3 つの追加構成およびバリアントのサポート。
-   VNet サービス エンドポイントが設定された Azure SQL DB
-   Managed Instance (MI)
-   エラスティック プール
-   クラシック VNet およびその上位の Azure Resource Manager Virtual Network (VNet) のサポート。クラシック VNet は将来廃止されます。これにより、Azure-SSIS 統合ランタイム (IR) を VNet サービス エンドポイント/MI/オンプレミスのデータ アクセスを使用して Azure SQL DB に構成された VNet に挿入および結合できます。詳しくは、https://docs.microsoft.com/en-us/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network をご覧ください。 
-   SSISDB に接続するための SQL 認証およびその上位の Azure Active Directory (AAD) のサポート。これにより、ADF マネージド サービス ID (MSI) で AAD 認証を使用できます。
-   独自のオンプレミスの SQL Server ライセンスの利用のサポート。Azure ハイブリッド特典 (AHB) オプションから実質的なコスト削減を得られます。
-   Azure-SSIS IR のエンタープライズ エディションのサポート。これにより、高度な機能やプレミアム機能、追加のコンポーネントや拡張機能をインストールするカスタム セットアップ、およびサード パーティ製のエコシステムを使用できます。詳しくは、https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/ をご覧ください。 
-   ADF と SSIS のより深い統合。これにより、ADF パイプラインのファースト クラスの SSIS パッケージ実行アクティビティを呼び出し/トリガーし、SSMS でそれらのスケジュールを設定できます。詳しくは、https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/ をご覧ください。 


## <a name="what-is-integration-runtime"></a>統合ランタイムの概要
統合ランタイムは、さまざまなネットワーク環境間で以下のデータ統合機能を提供するために Azure Data Factory によって使用されるコンピューティング インフラストラクチャです。

- **データ移動**: データ移動については、統合ランタイムは移動元と移動先のデータ ストア間でデータを移動しながら、ビルトイン コネクタ、形式変換、列マッピング、高性能でスケーラブルなデータ転送をサポートします。
- **ディスパッチ アクティビティ**: 変換については、統合ランタイムは SSIS パッケージをネイティブに実行する機能を提供します。
- **SSIS パッケージの実行**: 管理されている Azure コンピューティング環境で SSIS パッケージをネイティブに実行します。 Integration Runtime では、Azure HDInsight、Azure Machine Learning、Azure SQL Database、SQL Server などのさまざまなコンピューティング サービスで実行される変換アクティビティのディスパッチや監視もサポートされています。

データの移動と変換に必要な 1 つ以上の統合ランタイム インスタンスをデプロイできます。 統合ランタイムは、Azure パブリック ネットワークまたはプライベート ネットワーク (オンプレミス、Azure Virtual Network、またはアマゾン ウェブ サービス Virtual Private Cloud (VPC)) で実行できます。 

詳細については、「[Azure Data Factory の統合ランタイム](concepts-integration-runtime.md)」を参照してください。

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>Integration Runtime 数の制限
1 つのデータ ファクトリに含めることができる統合ランタイム インスタンスの数に厳密な制限はありません。 ただし、SSIS パッケージの実行について、1 サブスクリプションにつき統合ランタイムが使用できる VM コア数には制限があります。 詳細については、「[Data Factory の制限](../azure-subscription-service-limits.md#data-factory-limits)」を参照してください。

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Azure Data Factory の最上位の概念とは
1 つの Azure サブスクリプションで 1 つ以上の Azure Data Factory インスタンス (データ ファクトリ) を利用できます。 Azure Data Factory には、プラットフォームとして連携する 4 つの主要コンポーネントが含まれます。このプラットフォームを基盤とし、データ移動とデータ変換のステップを含んだデータ主導型のワークフローを作成することができます。

### <a name="pipelines"></a>パイプライン
データ ファクトリは、1 つまたは複数のパイプラインを持つことができます。 パイプラインは、1 つの作業単位を実行するための複数のアクティビティから成る論理的なグループです。 パイプライン内のアクティビティがまとまって 1 つのタスクを実行します。 たとえば、Azure BLOB からデータを取り込み、HDInsight クラスターで Hive クエリを実行してデータを分割するアクティビティのグループをパイプラインに含めることができます。 パイプラインには、アクティビティをセットとして管理でき、個別に管理せずに済むというメリットがあります。 パイプライン内のアクティビティは、連鎖して順次処理することも、独立して並行処理することもできます。

### <a name="activity"></a>アクティビティ
アクティビティは、パイプライン内の処理ステップを表します。 たとえば、"*コピー*" アクティビティを使用して、データ ストア間でデータをコピーできます。 同様に、Azure HDInsight クラスターに対して Hive クエリを実行する Hive アクティビティを使用して、データを変換または分析できます。 Data Factory では、データ移動アクティビティ、データ変換アクティビティ、制御アクティビティの 3 種類のアクティビティがサポートされています。

### <a name="datasets"></a>データセット
データセットは、データ ストア内のデータ構造を表しています。アクティビティ内でデータを入力または出力として使用したい場合は、そのデータをポイントまたは参照するだけで済みます。 

### <a name="linked-services"></a>リンクされたサービス
リンクされたサービスは、接続文字列によく似ており、Data Factory が外部リソースに接続するために必要な接続情報を定義します。 リンクされたサービスはデータ ソースへの接続を定義するもので、データ セットはデータの構造を表すもの、と捉えることができます。 たとえば、Azure Storage のリンクされたサービスでは、Azure Storage アカウントに接続するための接続文字列を指定します。 また、Azure BLOB データ セットでは、データを格納する BLOB コンテナーやフォルダーを指定します。

Data Factory では、リンクされたサービスは 2 つの目的に使用されます。

- オンプレミスの SQL Server インスタンス、Oracle データベース インスタンス、ファイル共有、Azure Blob Storage アカウント、その他の "*データ ストア*" を表すため。 サポートされるデータ ストアの一覧については、「[Azure Data Factory のコピー アクティビティ](copy-activity-overview.md)」を参照してください。
- アクティビティの実行をホストできる *コンピューティング リソース* を表すため。 たとえば、HDInsight Hive アクティビティは HDInsight Hadoop クラスターで実行されます。 変換アクティビティとサポートされているコンピューティング環境の一覧については、「[Azure Data Factory でデータを変換する](transform-data.md)」を参照してください。

### <a name="triggers"></a>トリガー
トリガーは、パイプラインの実行をいつ開始するかを決定する処理単位を表します。 さまざまな種類のイベントに合わせて、さまざまな種類のトリガーがあります。 

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

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Data Factory の最新情報を入手するにはどうすればよいですか。
Azure Data Factory の最新情報を入手するには、次のサイトを参照してください。

- [ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [ドキュメントのホーム ページ](/azure/data-factory)
- [製品のホーム ページ](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>技術的な詳細情報 

### <a name="how-can-i-schedule-a-pipeline"></a>パイプラインのスケジュールを設定するにはどうすればよいですか。 
パイプラインのスケジュールを設定するには、スケジューラのトリガーか時間ウィンドウ トリガーを使用できます。 トリガーでは、実時間カレンダー スケジュールが使用されます。これを使用して、定期的に、またはカレンダーベースの周期パターン (たとえば、毎週月曜日午後 6 時と木曜日午後 9 時など) を使用してパイプラインをスケジュールできます。 詳細については、[パイプラインの実行とトリガー](concepts-pipeline-execution-triggers.md)に関するページを参照してください。

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>実行されるパイプラインにパラメーターを渡すことはできますか。
はい。パラメーターは ADF でファースト クラスの最上位の概念です。 パイプライン レベルでパラメーターを定義し、パイプライン実行をオンデマンドで実行するときまたはトリガーを使用して実行するときに、引数を渡すことができます。  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>パイプライン パラメーターの既定値は定義できますか? 
はい。 パイプラインではパラメーターの既定値を定義できます。 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>パイプラインのアクティビティはパイプライン実行に渡される引数を使用できますか? 
はい。 パイプライン内の各アクティビティは、パイプラインに渡されて `@parameter` コンストラクトで実行されるパラメーター値を使用できます。 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>アクティビティの出力プロパティは別のアクティビティで使用できますか? 
はい。 アクティビティの出力は、`@activity` コンストラクトを使用して後続のアクティビティで使用できます。
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>アクティビティの出力で null 値を適切に処理するにはどうすればよいですか? 
式で `@coalesce` コンストラクトを使用すると、null 値を適切に処理できます。 

## <a name="next-steps"></a>次の手順
データ ファクトリを作成する手順については、次のチュートリアルをご覧ください。

- [クイックスタート: データ ファクトリを作成する](quickstart-create-data-factory-dot-net.md)
- [チュートリアル: クラウドのデータをコピーする](tutorial-copy-data-dot-net.md)
