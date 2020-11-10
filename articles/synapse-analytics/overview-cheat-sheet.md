---
title: チート シート - Azure Synapse Analytics (ワークスペース プレビュー)
description: Azure Synapse Analytics をユーザーに紹介するリファレンス ガイド
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 04/15/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: c8ae47f89dcddd2253abd21239eb1a9bffee27c7
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2020
ms.locfileid: "93307824"
---
# <a name="azure-synapse-analytics-cheat-sheet"></a>Azure Synapse Analytics のチート シート

[!INCLUDE [preview](includes/note-preview.md)]

Azure Synapse Analytics のチート シートでは、サービスの基本的な概念と重要なコマンドについて説明します。 この記事は、新しい学習者と、Azure Synapse の重要なトピックのハイライトを希望する学習者の両方に役立ちます。

## <a name="basics"></a>基本

**Synapse ワークスペース** は、Azure でクラウドベースのエンタープライズ分析を行うための、セキュリティ保護可能なコラボレーション境界です。 ワークスペースは、特定のリージョンにデプロイされ、関連付けられた ADLS Gen2 アカウントと (一時データを格納するための) ファイル システムを持ちます。 ワークスペースは、リソース グループに属します。

ワークスペースを使用すると、SQL および Apache Spark で分析できます。 SQL および Spark の分析に利用できるリソースは SQL および Spark **プール** にまとめられます。 

## <a name="synapse-sql"></a>Synapse SQL

**Synapse SQL** は、Synapse ワークスペースで T-SQL に基づいて分析するための機能です。 Synapse SQL には、専用とサーバーレスという 2 つの消費モデルがあります。  専用モデルの場合、 **専用の SQL プール** を使用します。 ワークスペースには、このようなプールをいくらでも与えることができます。 サーバーレスモデルを使用するには、 **サーバーレス SQL プール** を使用します。 ワークスペースごとにこのようなプールが 1 つ与えられます。

## <a name="apache-spark-for-synapse"></a>Synapse 用の Apache Spark

Spark 分析を使用するには、Synapse ワークスペースで **サーバーレス Apache Spark プール** を作成し、使用します。

## <a name="sql-terminology"></a>SQL の用語
| 期間                         | 定義      |
|:---                                 |:---                 |
| **SQL 要求**  |   専用 SQL プールまたはサーバーレス SQL プールを介して実行されるクエリなどの操作。 |

## <a name="spark-terminology"></a>Spark の用語
| 期間                         | 定義      |
|:---                                 |:---                 |
|**Synapse 用の Apache Spark** | サーバーレス Spark プールで使用される Spark ランタイム。 現在サポートされているバージョンは、Python 3.6.1、Scala 2.11.12、Apache Spark 0.5 向け .NET サポート、および Delta Lake 0.3 を使用する Spark 2.4 です。  | 
| **Apache Spark プール**  | 対応するデータベースをワークスペースにデプロイできる、0 対 N の Spark プロビジョニング済みのリソース。 Spark プールは、自動で一時停止、再開、およびスケーリングできます。  |
| **Spark アプリケーション**  |   ドライバー プロセスと Executor プロセスのセットで構成されます。 Spark アプリケーションはサーバーレス Spark プールで実行されます。            |
| **Spark セッション**  |   Spark アプリケーションの統合エントリ ポイント。 これにより、Spark のさまざまな機能との対話、および少数のコンストラクトとの対話が可能になります。 ノートブックを実行するには、セッションを作成する必要があります。 セッションは、特定のサイズの特定の数の Executor で実行するように構成できます。 ノートブック セッションの既定の構成では、2 つの中規模の Executor で実行されます。 |
|**データの統合**| さまざまなソース間でデータを取り込み、ワークスペース内またはワークスペース外で実行されるアクティビティを調整する機能を提供します。| 
|**アイテム**| ユーザーがデータ ソースの管理、開発、調整、および視覚化を行うために必要なすべてのオブジェクトをカプセル化する概念。|
|**ノートブック**| Scala、PySpark、C#、および SparkSQL をサポートする、インタラクティブかつリアクティブなデータ サイエンスおよびエンジニアリング インターフェイス。 |
|**Spark ジョブ定義**|コードとその依存関係を含むアセンブリ jar を使用して Spark ジョブを送信するためのインターフェイス。|
|**データ フロー**|  ビッグ データの変換を行うために、コーディングを必要としない、完全に視覚的なエクスペリエンスを提供します。 すべての最適化と実行は、サーバーレス方式で処理されます。 |
|**SQL スクリプト**| ファイルに保存された SQL コマンドのセット。 1 つの SQL スクリプトに 1 つまたは複数の SQL ステートメントを含めることができます。 専用 SQL プールまたはサーバーレス SQL プールを介して SQL 要求を実行するために使用できます。|
|**パイプライン**| 1 つのタスクを連携して実行するアクティビティの論理的なグループ。|
|**アクティビティ**| データのコピー、ノートブックや SQL スクリプトの実行など、データに対して実行するアクションを定義します。|
|**トリガー**| パイプラインを実行します。 手動または自動で実行できます (スケジュール、タンブリング ウィンドウ、またはイベントベース)。|
|**リンクされたサービス**| ワークスペースが外部リソースに接続するために必要な接続情報を定義する接続文字列。|
|**データセット**|  アクティビティで入力および出力として使用されるデータを単にポイントまたは参照する、データの名前付きビュー。 リンクされたサービスに属します。|

## <a name="next-steps"></a>次のステップ

- [ワークスペースを作成する](quickstart-create-workspace.md)
- [Synapse Studio を使用する](quickstart-synapse-studio.md)
- [専用 SQL プールを作成する](quickstart-create-sql-pool-portal.md)
- [サーバーレス Apache Spark プールを作成する](quickstart-create-apache-spark-pool-portal.md)
- [サーバーレス SQL プールを使用する](quickstart-sql-on-demand.md)

