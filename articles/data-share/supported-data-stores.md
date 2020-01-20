---
title: Azure Data Share でサポートされているデータ ストア
description: Azure Data Share での使用がサポートされているデータ ストアについて説明します。
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 56103ed89d2e7813fd60bc50ecca7271f5421a4a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438687"
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
| Azure Data Explorer | |[限定プレビュー](https://aka.ms/azuredatasharepreviewsignup) |

## <a name="data-store-support-matrix"></a>データ ストアのサポート マトリックス

Azure Data Share では、データ コンシューマーがデータを受け入れるデータ ストアを柔軟に決定できます。 たとえば、Azure SQL Database から共有されているデータは、Azure Data Lake Store Gen2、Azure SQL Database、または Azure Synapse Analytics に受信できます。 お客様は、受信したデータ共有を構成するときに、データを受信する形式を選択できます。 

次の表では、データ共有を受け入れて構成する際にデータ コンシューマーが利用できるさまざまな組み合わせと選択肢の詳細を示しています。 データセット マッピングを構成する方法の詳細については、「[データセット マッピングを構成する方法](how-to-configure-mapping.md)」を参照してください。

|  | Azure Blob Storage | Azure SQL Data Lake Gen1 | Azure SQL Data Lake Gen2 | Azure SQL データベース | Azure Synapse Analytics 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure BLOB ストレージ |✓ ||✓|
| Azure Data Lake Storage Gen1 |✓ | |✓|
| Azure Data Lake Storage Gen2 |✓ | |✓|
| Azure SQL データベース |✓ | |✓|✓|✓|
| Azure Synapse Analytics |✓ | |✓|✓|✓|

## <a name="next-steps"></a>次のステップ

データの共有を始める方法については、[データの共有](share-your-data.md)に関するチュートリアルをご覧ください。
