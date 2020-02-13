---
title: Azure Data Share でサポートされているデータ ストア
description: Azure Data Share での使用がサポートされているデータ ストアについて説明します。
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 5d4b1282b0a08657aea6f8a13aae7ed1fe49079b
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964211"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Azure Data Share でサポートされているデータ ストア

Azure Data Share は、さまざまなデータ ストアとの間で共有する機能など、オープンで柔軟なデータ共有を提供します。 データ プロバイダーは、1 つの種類のデータ ストアからデータを共有できます。また、データ コンシューマーは、データを受信するデータ ストアを選択できます。 

この記事では、Azure Data Share でサポートされている Azure データ ストアの豊富なセットについて説明します。 また、データ プロバイダーとデータ コンシューマーが活用できるデータ ストアの組み合わせに関する情報も確認できます。 

## <a name="what-data-stores-are-supported-in-azure-data-share"></a>Azure Data Share でサポートされているデータ ストア 

次の表は、Azure Data Share でサポートされているデータ ソースの詳細を示しています。 

| データ ストア | スナップショット ベースの共有 | インプレース共有 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure BLOB ストレージ |✓ | |
| Azure Data Lake Storage Gen1 |✓ | |
| Azure Data Lake Storage Gen2 |✓ ||
| Azure SQL データベース |パブリック プレビュー | |
| Azure Synapse Analytics (以前の Azure SQL DW) |パブリック プレビュー | |
| Azure Data Explorer | |パブリック プレビュー |

## <a name="data-store-support-matrix"></a>データ ストアのサポート マトリックス

Azure Data Share では、データ コンシューマーがデータを受け入れるデータ ストアを柔軟に決定できます。 たとえば、Azure SQL Database から共有されているデータは、Azure Data Lake Store Gen2、Azure SQL Database、または Azure Synapse Analytics に受信できます。 お客様は、受信したデータ共有を構成するときに、データを受信する形式を選択できます。 

次の表では、データ共有を受け入れて構成する際にデータ コンシューマーが利用できるさまざまな組み合わせと選択肢の詳細を示しています。 データセット マッピングを構成する方法の詳細については、「[データセット マッピングを構成する方法](how-to-configure-mapping.md)」を参照してください。

|  | Azure Blob Storage | Azure Data Lake Storage Gen1 | Azure Data Lake Storage Gen2 | Azure SQL データベース | Azure Synapse Analytics 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure BLOB ストレージ | ✓ || ✓|
| Azure Data Lake Storage Gen1 | ✓ | | ✓|
| Azure Data Lake Storage Gen2 | ✓ | | ✓|
| Azure SQL データベース | ✓ | | ✓| ✓| ✓|
| Azure Synapse Analytics (以前の Azure SQL DW) | ✓ | | ✓| ✓| ✓|

## <a name="share-from-a-storage-account"></a>ストレージ アカウントからの共有
Azure Data Share では、Azure Data Lake Gen1 および Azure Data Lake Gen2 からのファイル、フォルダー、ファイル システムの共有がサポートされています。 また、Azure Blob Storage からの BLOB、フォルダー、コンテナーの共有もサポートされています。 スナップショット ベースの共有でフォルダーが共有されている場合、データ コンシューマーは、共有データの完全なコピーを作成するか、増分スナップショット機能を利用して新規または更新されたファイルのみをコピーするか選択できます。 同じ名前の既存のファイルは上書きされます。

## <a name="share-from-a-sql-based-source"></a>SQL ベースのソースからの共有
Azure Data Share では、Azure SQL Database および Azure Synapse Analytics (旧称 Azure SQL DW) からのテーブルまたはビューの共有がサポートされています。 データ コンシューマーは、データを Azure Data Lake Storage Gen2 または Azure Blob Storage に、csv または parquet ファイルとして受け入れることができます。 完全スナップショットを使用すると、ターゲット ファイルの内容が上書きされます。 また、データ コンシューマーはデータを SQL テーブルに受け入れることもできます。 ターゲット SQL テーブルがデータ コンシューマー側で使用できない場合は、Azure Data Share によって送信元スキーマを使って SQL テーブルが作成されます。 完全スナップショットを使用すると、ソース テーブルの内容がターゲット SQL テーブルに追加されます。 増分スナップショットは現在サポートされていません。

## <a name="share-from-azure-data-explorer"></a>Azure Data Explorer からの共有
Azure Data Share では、Azure Data Explorer クラスターからデータベースをインプレース共有する機能がサポートされています。 データ プロバイダーは、データベース レベルまたはクラスター レベルで共有することができます。 データベース レベルで共有された場合、データ コンシューマーは、データ プロバイダーが共有した特定のデータベースにのみアクセスできます。 クラスター レベルで共有された場合、データ コンシューマーは、データ プロバイダーが今後作成するデータベースも含めて、プロバイダーのクラスターからすべてのデータベースにアクセスできます。

共有データベースにアクセスするには、データ コンシューマーが独自の Azure Data Explorer クラスターを持っている必要があります。 データ コンシューマーの Azure Data Explorer クラスターは、データ プロバイダーの Azure Data Explorer クラスターと同じ Azure データ センターに配置されている必要があります。 共有関係が確立されると、Azure Data Share によって、プロバイダーとコンシューマーの Azure Data Explorer クラスターの間にシンボリック リンクが作成されます。

Azure Data Explorer では、2 つのデータ インジェスト モードがサポートされています。バッチとストリーミングです。 共有データベースのバッチから受信したデータは、数秒から数分の間にデータ コンシューマー側に表示されます。 ストリーミングから受信したデータがデータ コンシューマー側に表示されるまでには、最大 24 時間かかることがあります。 

## <a name="next-steps"></a>次のステップ

データの共有を始める方法については、[データの共有](share-your-data.md)に関するチュートリアルをご覧ください。
