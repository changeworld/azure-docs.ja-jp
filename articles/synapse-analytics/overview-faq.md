---
title: FAQ - Azure Synapse Analytics (ワークスペース プレビュー)
description: Azure Synapse Analytics (ワークスペース プレビュー) の FAQ
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 04/15/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 530ebe09ee0ca44b0a5fbd4b8da2784e2c7ae7ea
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843895"
---
# <a name="azure-synapse-analytics-workspaces-preview-frequently-asked-questions"></a>Azure Synapse Analytics (ワークスペース プレビュー) のよく寄せられる質問

このガイドでは、Synapse Analytics についてよく寄せられる質問を紹介します。

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="general"></a>全般

### <a name="q-what-is-azure-synapse-analytics"></a>Q:Azure Synapse Analytics とは何ですか

A:Azure Synapse は、BI、AI、および継続的インテリジェンスのための統合データ プラットフォームです。 一元的な方法で以下を実行できる単一のプラットフォームを通じて、SQL や Apache Spark などのさまざまな分析ランタイムに接続します。

- ネットワークなどの分析リソースをセキュリティで保護し、プール、データ、開発の成果物へのシングル サインオン アクセスを管理する。
- すべてのレイヤーのワークスペース アクティビティで発生するイベントを容易に監視し、最適化、対応、デバッグをすばやく行う。
- エンジンをまたいでメタデータを管理する。 サーバーレス Apache Spark プール テーブルを作成すると、ご自分の Azure Synapse データベースでそれを自動的に使用できるようになります。
- 一元的なユーザー エクスペリエンスを通じてデータを操作する。 Synapse Studio では、ビッグ データ開発者、データ エンジニア、DBA、データ アナリスト、データ サイエンティストが同じプラットフォームに集約されます。

詳しくは、[Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics/overview-what-is) に関する記事をご覧ください。

### <a name="q-what-are-the-main-components-of-azure-synapse-analytics"></a>Q:Azure Synapse Analytics の主なコンポーネントは何ですか

A:Azure Synapse には次の機能があります。

- 分析機能は、専用 SQL プールまたはサーバーレス SQL プール (プレビュー) を通じて提供されます。
- Scala、Python、Spark、SQL、C# が完全にサポートされるサーバーレス Apache Spark プール (プレビュー)
- コーディング不要のビッグ データ変換エクスペリエンスを提供する Data Flow
- データを統合し、コード開発のすべてを運用化できるデータ統合およびオーケストレーション
- 単一の Web UI を通じてこれらのすべての機能にアクセスできる Studio

### <a name="q-how-does-azure-synapse-analytics-relate-to-azure-sql-data-warehouse"></a>Q:Azure Synapse Analytics と Azure SQL Data Warehouse はどのような関係ですか

A:Azure Synapse Analytics は Azure SQL Data Warehouse を分析プラットフォームへと進化させたもので、専用 SQL プールをデータ ウェアハウス ソリューションとして備えています。 このプラットフォームは、データの探索、インジェスト、変換、準備、およびサービスを提供する分析レイヤーを兼ね備えています。

## <a name="use-cases"></a>ユース ケース

### <a name="q-how-do-i-rename-a-published-artifact-dataset-notebook-sql-script-and-so-on-in-azure-synapse"></a>Q:Azure Synapse では、どのように公開されているアーティファクト (データセット、ノートブック、SQL スクリプトなど) を名前変更するのですか

A:公開されているアーティファクト ファイルを名前変更するには、まずファイルを複製し、新しいファイルの名前を好みの名前に変更します。 アーティファクトを参照しているすべてのファイルの名前を、手動で新しいものに更新し、古いファイルを削除する必要があります。

### <a name="q-what-is-a-good-use-case-for-dedicated-sql-pool"></a>Q:専用 SQL プールに適したユース ケースを教えてください

A:専用 SQL プールは、お客様のデータ ウェアハウス ニーズを満たすうえで中心的な役割を果たします。 [価格とパフォーマンス](https://azure.microsoft.com/services/sql-data-warehouse/compare/)の点で優れたデータ ウェアハウス ソリューションです。 専用 SQL プールは業界をリードするクラウド データ ウェアハウス ソリューションであり、以下のことが可能です。

- 高いコンカレンシーとワークロード分離により、パフォーマンスに影響を与えることなく大規模で多様なワークロードを提供する
- ネットワーク セキュリティからきめ細かいアクセス制御まで幅広い高度な機能を通じてデータを容易にセキュリティ保護する
- 広範なエコシステムを活用する

### <a name="q-what-is-a-good-use-case-for-serverless-apache-spark-pool-in-azure-synapse"></a>Q:Azure Synapse におけるサーバーレス Apache Spark プールの適切なユース ケースを教えてください

A:Microsoft の第一の目的は、バッチまたはストリームでレイク全体のデータを変換するための優れたデータ エンジニアリング エクスペリエンスを提供することです。 データ オーケストレーションへの緊密でシンプルな統合により、開発作業の運用化を簡単に行えます。

Apache Spark の別のユース ケースとして、データ サイエンティストは以下を実行できます。

- 特徴の抽出
- データの探索
- モデルのトレーニング

### <a name="q-what-is-a-good-use-case-for-serverless-sql-pool-in-azure-synapse"></a>Q:Azure Synapse におけるサーバーレス SQL プールの適切なユース ケースを教えてください

A:サーバーレス SQL プールは、データ レイク内のデータに対するクエリ サービスです。 使い慣れた T-SQL 構文を使ってインプレースでデータのクエリを実行できるため、すべてのデータへのアクセスを民主化できます。データを専用のストアにコピーしたり読み込んだりする必要はありません。

ユース ケースには次のような例があります。

- 基本の検出と探索 - データ アナリスト、新興のデータ サイエンティスト、データ エンジニアが、読み取り T-SQL クエリのスキーマを使用し、データ レイク内に存在するデータの最初の分析情報を簡単に得られるようにします
- 論理データ ウェアハウス - データ アナリストは T-SQL 言語のすべての表現を実行し、Azure Storage 内に存在するデータに対してクエリを直接実行して分析できます。また、使い慣れた BI ツール (Azure Analysis Services、Power BI Premium など) を使用し、Starlight Query クエリを再実行してダッシュボードを更新できます
- "単一クエリ" ETL - データ エンジニアが Azure Storage ベースのデータをある形式から別の形式に変換し、大規模な並列処理でフィルターや集計などを行い、クエリ結果を Azure Storage に保持して、Starlight Query や関心のある他のサービスですぐに追加の処理を行えるようにできます

### <a name="q-what-is-a-good-use-case-for-data-flow-in-azure-synapse"></a>Q:Azure Synapse におけるデータ フローの適切なユース ケースを教えてください

A:データ フローを使用すると、データ エンジニアは、コードを記述することなくグラフィカルなデータ変換ロジックを開発できます。 生成されたデータ フローは、データ統合およびオーケストレーション内でアクティビティとして実行されます。 データ フロー アクティビティは、既存のスケジュール設定、制御、フロー、および監視機能を通して運用化できます。

## <a name="security-and-access"></a>セキュリティとアクセス

A:エンドツーエンドのシングル サインオン エクスペリエンスが、Synapse Analytics において重要な認証プロセスです。 完全な Azure AD 統合を通じた ID の管理とパススルーは必須です。

### <a name="q-how-do-i-get-access-to-files-and-folders-in-the-adls-gen2"></a>Q:ADLS Gen2 のファイルとフォルダーにアクセスするにはどうすればよいですか

A:ファイルとフォルダーへのアクセスは現在、ADLS Gen2 を通じて管理されています。 詳しくは、[Data Lake ストレージのアクセス制御](../storage/blobs/data-lake-storage-access-control.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)に関するページを参照してください。

### <a name="q-can-i-use-third-party-business-intelligence-tools-to-access-azure-synapse-analytics"></a>Q:Azure Synapse Analytics へのアクセスにサードパーティのビジネス インテリジェンス ツールを使用できますか

A:はい。Tableau や Power BI など、サードパーティのビジネス アプリケーションを使用して、専用 SQL プールとサーバーレス SQL プールに接続できます。 Spark では IntelliJ がサポートされています。

## <a name="next-steps"></a>次のステップ

* [Azure Synapse Analytics の使用を開始する](get-started.md)
* [ワークスペースを作成する](quickstart-create-workspace.md)
* [サーバーレス SQL プールを使用する](quickstart-sql-on-demand.md)
