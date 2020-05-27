---
title: Azure SQL Edge (プレビュー) でサポートされる機能
description: Azure SQL Edge (プレビュー) でサポートされる機能の詳細
keywords: SQL Edge の紹介, SQL Edge とは, SQL Edge の概要
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 7d33c2bef1cd0f7bfab4ec3c09f16c049881d1cd
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594621"
---
# <a name="supported-features-of-azure-sql-edge-preview"></a>Azure SQL Edge (プレビュー) でサポートされる機能 

この記事では、Azure SQL Edge でサポートされる機能の詳細について説明します。 Azure SQL Edge は、Linux 上の Microsoft SQL Server データベース エンジンの最新バージョンに基づいて構築されており、SQL Server 2019 on Linux/Windows で現在サポート対象外か利用不可となっているいくつかの機能に加えて、Linux 用 SQL Server 2019 でサポートされている機能のサブセットをサポートしています。 SQL Server on Linux でサポートされている機能の完全な一覧については、「[SQL Server 2019 on Linux のエディションとサポートされる機能](https://docs.microsoft.com/sql/linux/sql-server-linux-editions-and-components-2019)」を参照してください。 SQL Server on Windows のエディションとサポートされている機能については、「[Editions and supported features of SQL Server 2019 (15.x)](https://docs.microsoft.com/sql/sql-server/editions-and-components-of-sql-server-version-15)」(SQL Server 2019 (15.x) のエディションとサポートされる機能) を参照してください。

> [!NOTE]
> Azure SQL Edge は現在プレビュー段階であるため、運用環境では使用しないでください。 Microsoft では、デプロイとユース ケース シナリオの検証後に運用環境で Azure SQL Edge を実行することをお勧めします。

## <a name="azure-sql-edge-editions"></a>Azure SQL Edge のエディション

Azure SQL Edge は、2 つの異なるエディション (ソフトウェア プラン) で提供されています。 これらのエディションの機能セットはまったく同じで、使用権限と、ホスト システム上でアクセスできる CPU とメモリの量についてのみ違いがあります。

   |**プラン**  |**説明**  |
   |---------|---------|
   |Azure SQL Edge Developer  |  開発専用 SKU です。各Azure SQL Edge Developer コンテナーは、最大 4 コアおよび 32 GB メモリまでに制限されます。  |
   |Azure SQL Edge    |  運用 SKU です。各 Azure SQL Edge コンテナーは、最大 8 コアおよび 64 GB メモリまでに制限されます。  |

## <a name="operating-system"></a>オペレーティング システム

現在、Azure SQL Edge コンテナーは Ubuntu (16.04 および 18.04) をベースとしているため、Ubuntu 16.04 および 18.04 を実行している Docker ホスト上での実行のみがサポートされています。 (Docker CE または Docker EE を使用する) 他の Linux ディストリビューションや Windows など、他のオペレーティング システム ホスト上でも Azure SQL Edge を実行できますが、これらの構成は Microsoft によって広範にテストされていません。

Azure SQL Edge は現在、Azure IoT Edge を通じたデプロイでのみサポートされています。 Azure IoT Edge でサポートされているシステムの詳細については、「[Azure IoT Edge のサポートされるシステム](https://docs.microsoft.com/azure/iot-edge/support)」を参照してください。

Windows 上で Azure SQL Edge を実行するために推奨される構成は、Windows ホスト上で Ubuntu VM を構成し、Linux VM 内で SQL Edge を実行することです。

## <a name="hardware-support"></a>ハードウェア サポート

Azure SQL Edge には Intel、AMD、または ARM 製の 64 ビットプロセッサが必要であり、少なくとも 1 個のプロセッサと 1 GB の RAM がホストに搭載されている必要があります。 Azure SQL Edge の初期メモリ占有領域は 500 MB ほどですが、エッジ デバイス上で実行される他の IoT Edge モジュール用に追加のメモリが必要です。

## <a name="azure-sql-edge-components"></a>Azure SQL Edge のコンポーネント

Azure SQL Edge ではデータベース エンジンのみがサポートされており、SQL Server 2019 on Windows または with SQL Server 2019 on Linux で利用可能なその他のコンポーネントに対するサポートはありません。 具体的には、Analysis Services、Reporting Services、Integration Services、マスター データ サービス、Machine Learning Services (データベース内)、Machine Learning Server (スタンドアロン) のような SQL Server コンポーネントは、Azure SQL Edge ではサポートされません。

## <a name="supported-features"></a>サポートされている機能

Azure SQL Edge では、SQL Server on Linux の機能のサブセットに加えて、次の新機能もサポートされます。 

- Azure Stream Analytics で採用されているのと同じエンジンに基づく SQL ストリーミング。Azure SQL Edge でリアルタイムのデータ ストリーミング機能を提供します。 
- 時系列データ分析のための、新しい T-SQL 関数呼び出し Date_Bucket。
- SQL エンジンに付属する ONNX ランタイムを使用した機械学習機能。

## <a name="sql-server-on-linux-features-not-supported-in-azure-sql-edge"></a>Azure SQL Edge でサポートされていない SQL Server on Linux の機能

以下に、Azure SQL Edge で現在サポートされていない SQL Server 2019 on Linux の機能の一覧を示します。

| 領域 | サポートされていない機能またはサービス |
|-----|-----|
| **データベースの設計** | インメモリ OLTP と関連の DDL コマンドおよび Transact-SQL 関数、カタログ ビュー、動的管理ビュー |
| &nbsp; | HierarchyID データ型と関連の DDL コマンドおよび Transact-SQL 関数、カタログ ビュー、動的管理ビュー |
| &nbsp; | 空間データ型と関連の DDL コマンドおよび Transact-SQL 関数、カタログ ビュー、動的管理ビュー |
| &nbsp; | Stretch DB と関連の DDL コマンドおよび Transact-SQL 関数、カタログ ビュー、動的管理ビュー |
| &nbsp; | フルテキスト インデックス、フルテキスト検索と関連の DDL コマンドおよび Transact-SQL 関数、カタログ ビュー、動的管理ビュー|
| &nbsp; | FileTable、FILESTREAM と関連の DDL コマンドおよび Transact-SQL 関数、カタログ ビュー、動的管理ビュー|
| **データベース エンジン** | レプリケーション。 ただし、Azure SQL Edge をレプリケーション トポロジのプッシュ サブスクライバーとして構成できます。 |
| &nbsp; | PolyBase。 ただし、Azure SQL Edge を Polybase で外部テーブルのターゲットとして構成できます。 |
| &nbsp; | Java および Spark による言語拡張 |
| &nbsp; | Active Directory の統合 |
| &nbsp; | データベース スナップショット |
| &nbsp; | 永続メモリのサポート |
| &nbsp; | Microsoft 分散トランザクション コーディネーター |
| &nbsp; | Resource Governor と IO リソース管理 |
| &nbsp; | バッファー プール拡張 |
| &nbsp; | サード パーティの接続を使用した分散クエリ |
| &nbsp; | リンク サーバー |
| &nbsp; | システム拡張ストアド プロシージャ (XP_CMDSHELL など) |
| &nbsp; | CLR アセンブリと関連の DDL コマンドおよび Transact-SQL 関数、カタログ ビュー、動的管理ビュー |
| &nbsp; | CLR に依存する T-SQL 関数 (ASSEMBLYPROPERTY、FORMAT、PARSE、TRY_PARSE など) |
| &nbsp; | CLR に依存する日付と時刻のカタログ ビュー、関数、クエリ句 |
| &nbsp; | バッファー プール拡張 |
| &nbsp; | データベース メール |
| **SQL Server エージェント** |  サブシステム: CmdExec、PowerShell、キュー リーダー、SSIS、SSAS、SSRS |
| &nbsp; | 警告 |
| &nbsp; | 管理対象のバックアップ |
| **高可用性** | Always On 可用性グループ  |
| &nbsp; | 基本的な可用性グループ |
| &nbsp; | Always On フェールオーバー クラスター インスタンス |
| &nbsp; | データベース ミラーリング |
| &nbsp; | ホット アド メモリと CPU |
| **Security** | 拡張キー管理 |
| &nbsp; | Active Directory 統合|
| &nbsp; | セキュリティで保護されたエンクレーブのサポート|
| **サービス** | SQL Server Browser |
| &nbsp; | R および Python による機械学習 |
| &nbsp; | StreamInsight |
| &nbsp; | Analysis Services |
| &nbsp; | Reporting Services |
| &nbsp; | Data Quality Services |
| &nbsp; | マスター データ サービス |
| &nbsp; | Distributed Replay |
| **管理の容易性** | SQL Server ユーティリティ コントロール ポイント |

## <a name="next-steps"></a>次の手順

- [Azure SQL Edge のデプロイ](deploy-portal.md)
- [Azure SQL Edge の構成](configure.md)
- [SQL Server クライアント ツールを使用した Azure SQL Edge のインスタンスへの接続](connect.md)
- [Azure SQL Edge でのデータベースのバックアップと復元](backup-restore.md)
