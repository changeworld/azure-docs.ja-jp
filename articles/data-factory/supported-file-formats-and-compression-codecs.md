---
title: Azure Data Factory のコピー アクティビティでサポートされているファイル形式
titleSuffix: Azure Data Factory & Azure Synapse
description: このトピックでは、Azure Data Factory と Azure Synapse Analytics のコピー アクティビティでサポートされているファイル形式や圧縮コードについて説明します。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: jianleishen
ms.openlocfilehash: 63798523e11df8729dd87a7b179b34058a691bc5
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124798524"
---
# <a name="supported-file-formats-and-compression-codecs-by-copy-activity-in-azure-data-factory-and-azure-synapse-pipelines"></a>Azure Data Factory と Azure Synapse のパイプラインでのコピー アクティビティでサポートされているファイル形式と圧縮コーデック
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

*この記事は、次のコネクタに適用されます: [Amazon S3](connector-amazon-simple-storage-service.md)、[Amazon S3 Compatible Storage](connector-amazon-s3-compatible-storage.md)、[Azure Blob](connector-azure-blob-storage.md)、[Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、[Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、[Azure Files](connector-azure-file-storage.md)、[ファイル システム](connector-file-system.md)、[FTP](connector-ftp.md)、[Google Cloud Storage](connector-google-cloud-storage.md)、[HDFS](connector-hdfs.md)、[HTTP](connector-http.md)、[Oracle Cloud Storage](connector-oracle-cloud-storage.md)、[SFTP](connector-sftp.md)。*

[!INCLUDE [data-factory-v2-file-formats](includes/data-factory-v2-file-formats.md)] 

[コピー アクティビティ](copy-activity-overview.md)を使用すると、ファイル ベースの 2 つのデータ ストア間でファイルをそのままコピーできます。その場合、データはシリアル化または逆シリアル化なしで効率的にコピーされます。 

さらに、特定の形式のファイルを解析または生成することもできます。 たとえば、次の操作を実行できます。

* SQL Server データベースからデータをコピーし、Parquet 形式で Azure Data Lake Storage Gen2 に書き込む。
* オンプレミスのファイル システムからテキスト (CSV) 形式でファイルをコピーし、Azure BLOB ストレージに Avro 形式で書き込む。
* オンプレミスのファイル システムから zip 形式のファイルをコピーし、その場で圧縮解除して、抽出されたファイルを Azure Data Lake Storage Gen2 に書き込む。
* Azure BLOB ストレージから Gzip 圧縮テキスト (CSV) 形式でデータをコピーし、Azure SQL Database に書き込む。
* シリアル化/逆シリアル化または圧縮/展開を必要とする他の多くのアクティビティ。

## <a name="next-steps"></a>次のステップ

コピー アクティビティの他の記事を参照してください。

- [コピー アクティビティの概要](copy-activity-overview.md)
- [コピー アクティビティのパフォーマンス](copy-activity-performance.md)
