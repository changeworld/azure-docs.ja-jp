---
title: "Azure Data Factory のバージョン 1 と 2 の比較 | Microsoft Docs"
description: "この記事では、Azure Data Factory のバージョン 1 とバージョン 2 を比較します。"
services: data-factory
documentationcenter: 
author: kromerm
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/20/2017
ms.author: makromer
ms.openlocfilehash: f55348e9974de17c6d7e704e185f1ea9b21ff66e
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2017
---
# <a name="compare-azure-data-factory-versions-1-and-2"></a>Azure Data Factory のバージョン 1 と 2 の比較
この記事では、Azure Data Factory のバージョン 2 (V2) をバージョン 1 (V1) と比較します。 サービスの V1 の概要については、[Data Factory バージョン 1 の概要](v1/data-factory-introduction.md)に関するページを参照してください。V2 の概要については、[Data Factory バージョン 2 の概要](introduction.md)に関するページを参照してください。

## <a name="feature-comparison"></a>機能の比較
次の表は、V1 と V2 の機能を比較しています。 

| 機能 | V1 | V2 | 
| ------- | --------- | --------- | 
| データセット | アクティビティで入力および出力として使用するデータを参照するデータの名前付きビューです。 データセットは、テーブル、ファイル、フォルダー、ドキュメントなど、さまざまなデータ ストア内のデータを示します。 たとえば、Azure Blob データセットは、アクティビティによってデータが読み取られる、Blob Storage 内の BLOB コンテナーと BLOB フォルダーを示しています。<br/><br/>**可用性**で、データセットの処理時間枠スライス モデル (時間単位、日単位など) を定義します。 | データセットは V2 でも同じです。 ただし、データセットの**可用性**スケジュールを定義する必要はありません。 クロック スケジューラ パラダイムからパイプラインをスケジュールできるトリガー リソースを定義できます。 詳細については、「[トリガー](concepts-pipeline-execution-triggers.md#triggers)」と[データセット](concepts-datasets-linked-services.md)に関するページを参照してください。 | 
| リンクされたサービス | リンクされたサービスは、接続文字列によく似ており、Data Factory が外部リソースに接続するために必要な接続情報を定義します。 | Data Factory V1 と同じですが、Data Factory V2 Integration Runtime コンピューティング環境を利用するための新しい **connectVia** プロパティがあります。 詳細については、[統合ランタイム](concepts-integration-runtime.md)に関するページと、[Azure Blob Storage のリンクされたサービスのプロパティ](connector-azure-blob-storage.md#linked-service-properties)に関するページを参照してください。 |
| パイプライン | データ ファクトリは、1 つまたは複数のパイプラインを持つことができます。 パイプラインは、1 つのタスクを連携して実行するアクティビティの論理的なグループです。 パイプラインをスケジュールして実行するには、startTime、endTime、isPaused を使用していました。 | パイプラインは依然として、データに対して実行されるアクティビティのグループです。 ただし、パイプライン内のアクティビティのスケジュールは、新しいトリガー リソースに分割されています。 Data Factory V2 のパイプラインは、むしろ、トリガーを介して個別にスケジュールされる "ワークフロー単位" として考えることができます。 <br/><br/>Data Factory V2 では、パイプラインに実行時間の "枠" がありません。 Data Factory V1 の概念である startTime、endTime、isPaused は、Data Factory V2 にはなくなりました。 詳細については、[パイプラインの実行とトリガー](concepts-pipeline-execution-triggers.md)に関するページと、[パイプラインとアクティビティ](concepts-pipelines-activities.md)に関するページを参照してください。 |
| アクティビティ | アクティビティは、パイプライン内のデータに対して実行するアクションを定義します。 データ移動 (コピー アクティビティ) およびデータ変換アクティビティ (Hive、Pig、MapReduce など) がサポートされています。 | Data Factory V2 でも、アクティビティはパイプライン内の定義されたアクションです。 V2 では、新しい[制御フロー アクティビティ](concepts-pipelines-activities.md#control-activities)が導入されました。 これらのアクティビティは、制御フロー (ループおよび分岐) で使用します。 V1 でサポートされていたデータ移動およびデータ変換アクティビティは、V2 でもサポートされています。 V2 では、データセットを使用せずに変換アクティビティを定義できます。 |
| ハイブリッド データ移動とアクティビティのディスパッチ | 以前は[データ管理ゲートウェイ](v1/data-factory-data-management-gateway.md)と呼ばれ、オンプレミスとクラウドの間のデータ移動をサポートしていました。 これが、Integration Runtime と呼ばれるようになりました。| データ管理ゲートウェイは、セルフホステッド Integration Runtime と呼ばれるようになりました。 V1 と同じ機能を提供します。 <br/><br/> V2 の Azure-SSIS Integration Runtime では、クラウドでの SQL Server Integration Services (SSIS) パッケージのデプロイと実行もサポートされています。 詳細については、[統合ランタイム](concepts-integration-runtime.md)に関する記事を参照してください。|
| パラメーター | 該当なし | パラメーターは、パイプラインで定義されている、読み取り専用構成設定のキーと値のペアです。 パイプラインを手動で実行するときは、パラメーターの引数を渡すことができます。 スケジューラ トリガーを使用している場合は、トリガーでパラメーターの値を渡すこともできます。 パイプライン内のアクティビティは、パラメーターの値を使用します。  |
| 式 | Data Factory V1 では、データ選択クエリやアクティビティ/データセットのプロパティに関数やシステム変数を使用できます。 | Data Factory V2 では、JSON 文字列値の任意の場所で式を使用できます。 詳細については、[V2 の式と関数](control-flow-expression-language-functions.md)に関するページを参照してください。|
| パイプライン実行 | 該当なし | パイプライン実行の単一のインスタンスです。 たとえば、午前 8 時、午前 9 時、午前 10 時に実行するパイプラインがあるとします。 ここでは、パイプラインの 3 つの独立した実行 (パイプライン実行) があることになります。 各パイプライン実行には、一意のパイプライン実行 ID があります。それぞれが特定のパイプライン実行を一意に定義する GUID です。 パイプライン実行は、通常、パイプラインで定義されたパラメーターに引数を渡してインスタンス化されます。 |
| アクティビティの実行 | 該当なし | パイプライン内のアクティビティ実行のインスタンス。 | 
| トリガーの実行 | 該当なし | トリガー実行のインスタンス。 詳細については、[トリガー](concepts-pipeline-execution-triggers.md)に関するページを参照してください。 |
| スケジュール設定 | スケジュール設定は、パイプラインの開始/終了時間、およびデータセットの可用性に基づきます。 | 外部スケジューラを介したスケジューラ トリガーまたは実行。 詳細については、[パイプラインの実行とトリガー](concepts-pipeline-execution-triggers.md)に関するページを参照してください。 |

以降のセクションでは、バージョン 2 の機能の詳細について説明します。 

## <a name="control-flow"></a>制御フロー  
最新のデータ ウェアハウスの多様な統合フローとパターンをサポートするため、Data Factory V2 では、時系列データと関連付けられていない新しい柔軟なデータ パイプライン モデルが利用できます。 これまで実現できなかったもので、今回実現できるようになった一般的なフローのいくつかを次に紹介します。   

### <a name="chaining-activities"></a>連鎖するアクティビティ
バージョン 1 では、アクティビティの出力を別のアクティビティの入力として構成し、それらを連鎖させる必要がありました。 V2 では、パイプライン内のシーケンスでアクティビティを連鎖させることができます。 アクティビティ定義の **dependsOn** プロパティを使用して、アップストリーム アクティビティに連鎖させることができます。 詳細と例については、[パイプラインとアクティビティ](concepts-pipelines-activities.md#multiple-activities-in-a-pipeline)に関する記事と、[アクティビティの分岐と連鎖](tutorial-control-flow.md)に関する記事を参照してください。 

### <a name="branching-activities"></a>アクティビティの分岐
V2 では、パイプライン内でアクティビティを分岐できるようになりました。 [If-condition アクティビティ](control-flow-if-condition-activity.md)は、プログラミング言語における `if` ステートメントと同じ働きを持ちます。 条件が `true` に評価されたときの一連のアクティビティと `false` に評価されたときの一連のアクティビティが評価されます。 アクティビティの分岐の例については、[アクティビティの分岐と連鎖](tutorial-control-flow.md)に関するチュートリアルを参照してください。

### <a name="parameters"></a>パラメーター 
パイプライン レベルでパラメーターを定義し、パイプラインをオンデマンドで起動するかトリガーから起動するときに引数を渡すことができます。 アクティビティは、パイプラインに渡される引数を使用できます。 詳細については、[パイプラインとトリガー](concepts-pipeline-execution-triggers.md)に関するページを参照してください。 

### <a name="custom-state-passing"></a>カスタム状態の受け渡し
状態などのアクティビティ出力は、パイプライン内の後続のアクティビティで使用できます。 たとえば、アクティビティの JSON 定義で `@activity('NameofPreviousActivity').output.value` という構文を使用して、前のアクティビティの出力にアクセスできます。 この機能を使用すると、アクティビティ間で値を受け渡すワークフローを構築できます。

### <a name="looping-containers"></a>ループ コンテナー
[ForEach アクティビティ](control-flow-for-each-activity.md)は、パイプライン内の繰り返し制御フローを定義します。 このアクティビティは、コレクションを反復処理するために使用され、指定されたアクティビティをループで実行します。 このアクティビティのループの実装は、プログラミング言語の Foreach ループ構造に似ています。 

[Until](control-flow-until-activity.md) アクティビティは、プログラミング言語における do-until ループ構文と同じ働きを持ちます。 Until アクティビティでは、そこに関連付けられている条件が true に評価されるまで、一連のアクティビティがループ実行されます。 Data Factory では、until アクティビティのタイムアウト値を指定することができます。  

### <a name="trigger-based-flows"></a>トリガー ベースのフロー
パイプラインは、オンデマンドで、または実時間に合わせてトリガーできます。 トリガーの詳細については、[パイプラインとトリガー](concepts-pipeline-execution-triggers.md)に関する記事を参照してください。 

### <a name="invoke-a-pipeline-from-another-pipeline"></a>別のパイプラインからのパイプラインの呼び出し
[パイプラインの実行アクティビティ](control-flow-execute-pipeline-activity.md)を使用すると、Data Factory のパイプラインが別のパイプラインを呼び出すことができます。

### <a name="delta-flows"></a>差分フロー
ETL パターンの主要な使用例は、"差分読み込み" です。つまり、パイプラインの最後の反復以降に変更されたデータのみを読み込みます。 バージョン 2 の新機能 ([ルックアップ アクティビティ](control-flow-lookup-activity.md)、柔軟なスケジュール設定、制御フローなど) により、この使用例を自然な方法で実現できます。 詳細な手順については、[増分コピーのチュートリアル](tutorial-incremental-copy-powershell.md)のページを参照してください。

### <a name="other-control-flow-activities"></a>その他の制御フロー アクティビティ
Data Factory V2 でサポートされているその他の制御フロー アクティビティをいくつか次に示します。 

制御アクティビティ | 説明
---------------- | -----------
[ForEachActivity](control-flow-for-each-activity.md) | ForEach アクティビティは、パイプライン内の繰り返し制御フローを定義します。 このアクティビティは、コレクションを反復処理するために使用され、指定されたアクティビティをループで実行します。 このアクティビティのループの実装は、プログラミング言語の Foreach ループ構造に似ています。
[WebActivity](control-flow-web-activity.md) | Web アクティビティを使用すると、Data Factory パイプラインからカスタム REST エンドポイントを呼び出すことができます。 このアクティビティで使用したり、アクセスしたりするデータセットやリンクされたサービスを渡すことができます。 
[ルックアップ アクティビティ](control-flow-lookup-activity.md) | ルックアップ アクティビティを使用して、任意の外部ソースからレコード/テーブル名/値を読み取ったり検索したりできます。 この出力は、後続のアクティビティによってさらに参照できます。 
[メタデータの取得アクティビティ](control-flow-get-metadata-activity.md) | GetMetadata アクティビティを使用すると、Azure Data Factory で任意のデータのメタデータを取得できます。 
[Wait アクティビティ](control-flow-wait-activity.md) | パイプラインが、指定された時間待機 (一時停止) します。

## <a name="deploy-ssis-packages-to-azure"></a>SSIS パッケージを Azure にデプロイする 
SQL Server Integration Services (SSIS) ワークロードをクラウドに移動する場合は、データ ファクトリ V2 を作成し、Azure-SSIS Integration Runtime (IR) をプロビジョニングします。 Azure-SSIS IR は、クラウドでの SSIS パッケージ実行専用の、Azure VM (ノード) の完全に管理されたクラスターです。 Azure-SSIS IR のプロビジョニング後は、SSIS パッケージをオンプレミスの SSIS 環境にデプロイするために使用していたのと同じツールを使用できます。 たとえば、SQL Server Data Tools (SSDT) または SQL Server Management Studio (SSMS) を使用して、Azure 上のこのランタイムに SSIS パッケージをデプロイできます。 詳しい手順については、「[SQL Server Integration Services パッケージを Azure にデプロイする](tutorial-deploy-ssis-packages-azure.md)」を参照してください。 

## <a name="flexible-scheduling"></a>柔軟なスケジュール設定
Data Factory V2 では、データセットの可用性スケジュールを定義する必要はありません。 クロック スケジューラ パラダイムからパイプラインをスケジュールできるトリガー リソースを定義できます。 柔軟なスケジュール設定/実行モデルのために、トリガーからパイプラインにパラメーターを渡すこともできます。 Data Factory V2 では、パイプラインに実行時間の "枠" がありません。 Data Factory V1 の概念である startTime、endTime、isPaused は、Data Factory V2 にはなくなりました。 Data Factory V2 でパイプラインを構築してスケジュールする方法については、[パイプラインの実行とトリガー](concepts-pipeline-execution-triggers.md)に関するページを参照してください。

## <a name="support-for-more-data-stores"></a>より多くのデータ ストアのサポート
V2 では、V1 よりも多くのデータ ストアとの間でのデータ コピーがサポートされています。 サポートされているデータ ストアの一覧については、以下の記事を参照してください。

- [V1 - サポートされているデータ ストア](v1/data-factory-data-movement-activities.md#supported-data-stores-and-formats)
- [V2 - サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)

## <a name="support-for-on-demand-spark-cluster"></a>オンデマンドの Spark クラスターのサポート
V2 では、オンデマンドの HDInsight Spark クラスターの作成がサポートされています。 オンデマンドの Spark クラスターを作成するには、オンデマンドの HDInsight のリンクされたサービスに対する定義で、クラスターの種類を Spark として指定します。 その後、このリンクされたサービスを使用するように、パイプラインの Spark アクティビティを構成できます。 実行時、アクティビティが実行されるときに、Data Factory サービスによって自動的に Spark クラスターが作成されます。 詳細については、次の記事を参照してください。

- [V2 での Spark アクティビティ](transform-data-using-spark.md)
- [Azure HDInsight のオンデマンドのリンクされたサービス](compute-linked-services.md#azure-hdinsight-on-demand-linked-service)

## <a name="custom-activities"></a>カスタム アクティビティ
V1 では、IDotNetActivity インターフェイスの Execute メソッドを実装するクラスが含まれた .NET クラス ライブラリ プロジェクトを作成して (カスタム) DotNet アクティビティ コードを実装します。 そのため、カスタム コードは、.NET Framework 4.5.2 で記述し、Windows ベースの Azure Batch プール ノードで実行する必要があります。 

V2 のカスタム アクティビティでは、.NET インターフェイスを実装する必要はありません。 コマンドとスクリプトを直接実行できるようになり、実行可能ファイルとしてコンパイルされた独自のカスタム コードを実行できるようになりました。 

詳細については、[V1 と V2 でのカスタム アクティビティの違い](transform-data-using-dotnet-custom-activity.md#difference-between-custom-activity-in-azure-data-factory-v2-and-custom-dotnet-activity-in-azure-data-factory-v1)に関するページを参照してください。

## <a name="sdks"></a>SDK
Data Factory のバージョン 2 では、パイプラインの作成、管理、および監視に使用できる、より豊富な SDK セットが用意されています。

- **.NET SDK**: .NET SDK は V2 用に更新されています。 
- **PowerShell**: PowerShell コマンドレットは V2 用に更新されています。 V2 のコマンドレットは、名前に **DataFactoryV2** が含まれています。 たとえば、Get AzureRmDataFactoryV2 となります。 
- **Python SDK**: この SDK は、V2 で新しく導入されました。
- **REST API**: REST API は V2 用に更新されています。 

V2 で更新された SDK は、バージョン 1 クライアントと下位互換性がありません。 

## <a name="authoring-experience"></a>作成のエクスペリエンス
Data Factory V1 では、Azure Portal の Data Factory エディターを使用してパイプラインを作成できます。 現在、Data Factory V2 では、データ ファクトリの作成はプログラミング的な方法 (.NET SDK、REST API、PowerShell、Python など) でのみサポートされています。 ユーザー インターフェイスはまだサポートされていません。  Data Factory V1 では、SDK、REST、および PowerShell での作成機能もサポートされています。

## <a name="monitoring-experience"></a>監視のエクスペリエンス
V2 では、[Azure Monitor](monitor-using-azure-monitor.md) を使用してデータ ファクトリを監視することもできます。 新しい PowerShell コマンドレットでは、[統合ランタイム](monitor-integration-runtime.md)の監視がサポートされています。 V1 と V2 のどちらでも、Azure Portal から起動できる監視アプリケーションによる視覚的な監視がサポートされています。


## <a name="next-steps"></a>次のステップ
データ ファクトリの作成手順について、[PowerShell](quickstart-create-data-factory-powershell.md)、[.NET](quickstart-create-data-factory-dot-net.md)、[Python](quickstart-create-data-factory-python.md)、[REST API](quickstart-create-data-factory-rest-api.md) の各クイック スタートを参照してください。 
