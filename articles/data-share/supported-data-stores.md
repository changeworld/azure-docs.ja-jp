---
title: Azure Data Share でサポートされているデータ ストア
description: Azure Data Share での使用がサポートされているデータ ストアについて説明します。
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 762cea6dce3e0c6be3f5e977c5f9de806ca0880e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73511380"
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
| Azure SQL Database |パブリック プレビュー | |
| Azure SQL Data Warehouse |パブリック プレビュー | |
| Azure Data Explorer | |[限定プレビュー](https://aka.ms/azuredatasharepreviewsignup) |

## <a name="data-store-support-matrix"></a>データ ストアのサポート マトリックス

Azure Data Share では、データ コンシューマーがデータを受け入れるデータ ストアを柔軟に決定できます。 たとえば、Azure SQL Database から共有されているデータを、Azure Data Lake Store Gen2、Azure SQL Database、または Azure SQL Data Warehouse で受信できます。 お客様は、受信したデータ共有を構成するときに、データを受信する形式を選択できます。 

次の表では、データ共有を受け入れて構成する際にデータ コンシューマーが利用できるさまざまな組み合わせと選択肢の詳細を示しています。 データセット マッピングを構成する方法の詳細については、「[データセット マッピングを構成する方法](how-to-configure-mapping.md)」を参照してください。

|  | Azure Blob Storage | Azure SQL Data Lake Gen1 | Azure SQL Data Lake Gen2 | Azure SQL Database | Azure SQL Data Warehouse 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure BLOB ストレージ |✓ ||✓|
| Azure Data Lake Storage Gen1 |✓ | |✓|
| Azure Data Lake Storage Gen2 |✓ | |✓|
| Azure SQL Database |✓ | |✓|✓|✓|
| Azure SQL Data Warehouse |✓ | |✓|✓|✓|

## <a name="next-steps"></a>次の手順

データの共有を始める方法については、[データの共有](share-your-data.md)に関するチュートリアルをご覧ください。
