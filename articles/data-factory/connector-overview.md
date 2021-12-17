---
title: コネクタの概要
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory と Azure Synapse Analytics パイプラインでサポートされているコネクタについて説明します。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 10/14/2021
ms.author: jianleishen
ms.openlocfilehash: e766d41eb5145f91710c5f4e379bda73f83c0bc0
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130064642"
---
# <a name="azure-data-factory-and-azure-synapse-analytics-connector-overview"></a>Azure Data Factory と Azure Synapse Analytics のコネクタの概要

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory と Azure Synapse Analytics パイプラインでは、コピー、データ フロー、ルックアップ、メタデータの取得、および削除アクティビティで、次のデータ ストアおよび形式をサポートしています。 各データ ストアをクリックすると、サポートされている機能と対応する構成の詳細を確認できます。

## <a name="supported-data-stores"></a>サポートされているデータ ストア

[!INCLUDE [Connector overview](includes/data-factory-v2-connector-overview.md)]

## <a name="integrate-with-more-data-stores"></a>より多くのデータ ストアとの統合

Azure Data Factory と Synapse パイプラインでは、前述のリストよりも広範なデータ ストアを使用できます。 サービスの組み込みのコネクタの一覧に含まれていないデータ ストアとの間でデータを移動する必要がある場合の拡張可能ないくつかのオプションは次のとおりです。
- データベースとデータ ウェアハウスについては、通常、対応する ODBC ドライバを見つけることができ、これと共に[汎用 ODBC コネクタ](connector-odbc.md)を使用することができます。
- SaaS アプリケーションの場合:
    - RESTful API が提供されている場合は、[汎用 REST コネクタ](connector-rest.md)を使用できます。
    - OData フィードがある場合は、[汎用 OData コネクタ](connector-odata.md)を使用できます。
    - SOAP API が提供されている場合は、[汎用 HTTP コネクタ](connector-http.md)を使用できます。
    - ODBC ドライバーがある場合は、[汎用 ODBC コネクタ](connector-odbc.md)を使用できます。
- その他の場合は、サポートされている任意のデータ ストア (Azure Blob、File、FTP、SFTP など) として、データを読み込んだり、データを公開できるかを確認し、そこからサービスに取得させます。 [Azure Function](control-flow-azure-function-activity.md)、[カスタム アクティビティ](transform-data-using-dotnet-custom-activity.md)、[Databricks](transform-data-databricks-notebook.md)/[HDInsight](transform-data-using-hadoop-hive.md)、[Web アクティビティ](control-flow-web-activity.md)などを使用して、カスタム データ読み込みメカニズムを呼び出すことができます。

## <a name="supported-file-formats"></a>サポートされるファイル形式

次のファイル形式がサポートされています。 形式ベースの設定については、各記事を参照してください。

- [Avro 形式](format-avro.md)
- [バイナリ形式](format-binary.md)
- [Common Data Model 形式](format-common-data-model.md)
- [区切りテキスト形式](format-delimited-text.md)
- [差分形式](format-delta.md)
- [Excel 形式](format-excel.md)
- [JSON 形式](format-json.md)
- [ORC 形式](format-orc.md)
- [Parquet 形式](format-parquet.md)
- [XML 形式](format-xml.md)

## <a name="next-steps"></a>次のステップ

- [コピー アクティビティ](copy-activity-overview.md)
- [マッピング データ フロー](concepts-data-flow-overview.md)
- [ルックアップ アクティビティ](control-flow-lookup-activity.md)
- [メタデータの取得アクティビティ](control-flow-get-metadata-activity.md)
- [削除アクティビティ](delete-activity.md)
