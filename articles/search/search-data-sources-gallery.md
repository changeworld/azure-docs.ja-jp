---
title: データ ソース ギャラリー
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search インデックスにインポートするためにサポートされているすべてのデータ ソースを一覧表示します。
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
layout: LandingPage
ms.date: 06/23/2021
ms.openlocfilehash: 7cb78f108e53c93e736b11d49d21d8cd7c242b79
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130047078"
---
# <a name="data-sources-gallery"></a>データ ソース ギャラリー

検索インデックスへのデータ インジェストを簡略化するために、Microsoft またはパートナーのデータ コネクタを検索します。 この記事の内容は次のとおりです。

+ [Cognitive Search による一般公開のデータ ソース](#ga)
+ [Cognitive Search によるデータ ソースのプレビュー](#preview)
+ [Power Query コネクタ (プレビュー)](#powerquery)
+ [パートナーからのデータ ソース](#partners)

<a name="ga"></a>

## <a name="generally-available-data-sources-by-cognitive-search"></a>Cognitive Search による一般公開のデータ ソース

インデクサーと次のデータ ソース コネクタを使用して、他の Azure サービスからコンテンツを取り込みます。 

:::row:::
:::column span="":::

---

### <a name="azure-blob-storage"></a>Azure Blob Storage

[Cognitive Search](search-what-is-azure-search.md) を利用

BLOB のメタデータとコンテンツを抽出し、JSON ドキュメントにシリアル化して、検索ドキュメントとして検索インデックスにインポートします。 データ ソースとインデクサーの定義の両方でプロパティを設定して、さまざまな BLOB コンテンツの種類に合わせて最適化します。 変更の検出は自動的にサポートされます。

[[詳細]](search-howto-indexing-azure-blob-storage.md)

:::image type="icon" source="media/search-data-sources-gallery/azure_storage.png":::

:::column-end:::
:::column span="":::

---

### <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB (SQL API)

[Cognitive Search](search-what-is-azure-search.md) を利用

SQL API を介して Cosmos DB に接続し、コンテナーから項目を抽出し、JSON ドキュメントにシリアル化して、検索ドキュメントとして検索インデックスにインポートします。 変更の追跡を構成して、データベースの最新の変更で検索インデックスを更新します。

[[詳細]](search-howto-index-cosmosdb.md)

:::image type="icon" source="media/search-data-sources-gallery/azure_cosmos_db_logo_small.png":::

:::column-end:::
:::column span="":::

---

### <a name="azure-sql-database"></a>Azure SQL Database

[Cognitive Search](search-what-is-azure-search.md) を利用

1 つのテーブルまたはビューからフィールド値を抽出し、JSON ドキュメントにシリアル化して、検索ドキュメントとして検索インデックスにインポートします。 変更の追跡を構成して、データベースの最新の変更で検索インデックスを更新します。

[[詳細]](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

:::image type="icon" source="media/search-data-sources-gallery/azuresqlconnectorlogo_medium.png":::

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="azure-table-storage"></a>Azure Table Storage

[Cognitive Search](search-what-is-azure-search.md) を利用

Azure テーブルから行を抽出し、JSON ドキュメントにシリアル化して、検索ドキュメントとして検索インデックスにインポートします。 

[[詳細]](search-howto-indexing-azure-tables.md)

:::image type="icon" source="media/search-data-sources-gallery/azure_storage.png":::

:::column-end:::
:::column span="":::

---

### <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

[Cognitive Search](search-what-is-azure-search.md) を利用

Azure Data Laker Storage Gen2 を通じて Azure Storage に接続し、ディレクトリと入れ子になったサブディレクトリの階層からコンテンツを抽出します。

[[詳細]](search-howto-index-azure-data-lake-storage.md)

:::image type="icon" source="media/search-data-sources-gallery/azure_storage.png":::

:::column-end:::
:::column span="":::

---
':::column-end::': null
':::row-end::': null
':::row::': null
':::column span=""::':
  ':::column-end::': null
  ':::column span=""::': null
---

<a name="preview"></a>

## <a name="preview-data-sources-by-cognitive-search"></a>Cognitive Search によるデータ ソースのプレビュー

新しいデータ ソースは、プレビュー機能として発行されます。 始めるには[サインアップ](https://aka.ms/azure-cognitive-search/indexer-preview)します。

:::row:::
:::column span="":::

---

### <a name="cosmos-db-gremlin-api"></a>Cosmos DB (Gremlin API)

[Cognitive Search](search-what-is-azure-search.md) を利用

Gremlin API を介して Cosmos DB に接続し、コンテナーから項目を抽出し、JSON ドキュメントにシリアル化して、検索ドキュメントとして検索インデックスにインポートします。 変更の追跡を構成して、データベースの最新の変更で検索インデックスを更新します。

[[詳細]](search-howto-index-cosmosdb-gremlin.md)

:::image type="icon" source="media/search-data-sources-gallery/azure_cosmos_db_logo_small.png":::

:::column-end:::
:::column span="":::

---

### <a name="cosmos-db-mongo-api"></a>Cosmos DB (Mongo API)

[Cognitive Search](search-what-is-azure-search.md) を利用

Mongo API を介して Cosmos DB に接続し、コンテナーから項目を抽出し、JSON ドキュメントにシリアル化して、検索ドキュメントとして検索インデックスにインポートします。 変更の追跡を構成して、データベースの最新の変更で検索インデックスを更新します。

[[詳細]](search-howto-index-cosmosdb.md)

:::image type="icon" source="media/search-data-sources-gallery/azure_cosmos_db_logo_small.png":::

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="sharepoint-online"></a>SharePoint Online

[Cognitive Search](search-what-is-azure-search.md) を利用

同じテナント内のアカウントと検索サービスに対して、SharePoint オンライン サイトに接続し、1 つまたは複数のドキュメント ライブラリのドキュメントにインデックスを付けます。 既定でテキストおよび正規化された画像が抽出されます。 必要に応じて、コンテンツをさらに変換し、強化するためのスキルセットを構成したり、SharePoint で新しいコンテンツまたは変更されたコンテンツを使用して検索インデックスを更新するように変更の追跡を構成したりすることができます。

[[詳細]](search-howto-index-sharepoint-online.md)

:::image type="icon" source="media/search-data-sources-gallery/sharepoint_online_logo.png":::

:::column-end:::
:::column span="":::

---

### <a name="azure-mysql"></a>Azure MySQL

[Cognitive Search](search-what-is-azure-search.md) を利用

Azure 上の MySQL データベースに接続して、テーブル内の行を抽出し、JSON ドキュメントにシリアル化して、検索ドキュメントとして検索インデックスにインポートします。 後続の実行では、インデクサーは MySQL データベースのすべての変更、アップロード、および削除を取り込み、これらの変更を検索インデックスに反映します。

[[詳細]](search-howto-index-mysql.md)

:::image type="icon" source="media/search-data-sources-gallery/azure_mysql.png":::

:::column-end:::
:::column span="":::

---
':::column-end::': null
':::row-end::': null
':::row::': null
':::column span=""::':
  ':::column-end::': null
  ':::column span=""::': null
---

<a name="powerquery"></a>

## <a name="power-query-connectors-preview"></a>Power Query コネクタ (プレビュー)

インデクサーと Power Query コネクタをデータソースとして使用して、他のクラウド プラットフォーム上のデータに接続します。 始めるには[サインアップ](https://aka.ms/azure-cognitive-search/indexer-preview)します。

:::row:::
:::column span="":::

---

### <a name="amazon-redshift"></a>Amazon Redshift

[Power Query](/power-query/power-query-what-is-power-query) を利用

[Amazon Redshift](https://aws.amazon.com/redshift/) に接続し、Cognitive Search でインデックス作成のために検索可能なコンテンツを抽出します。

[[詳細]](search-how-to-index-power-query-data-sources.md)

:::column-end:::
:::column span="":::

---

### <a name="elasticsearch"></a>Elasticsearch

[Power Query](/power-query/power-query-what-is-power-query) を利用

クラウドの [Elasticsearch](https://www.elastic.co/elasticsearch) に接続し、Cognitive Search でインデックス作成のために検索可能なコンテンツを抽出します。

[[詳細]](search-how-to-index-power-query-data-sources.md)

:::column-end:::
:::column span="":::

---

### <a name="postgresql"></a>PostgreSQL

[Power Query](/power-query/power-query-what-is-power-query) を利用

クラウドの [PostgreSQL](https://www.postgresql.org/) データベースに接続し、Cognitive Search でインデックス作成のために検索可能なコンテンツを抽出します。

[[詳細]](search-how-to-index-power-query-data-sources.md)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="salesforce-objects"></a>Salesforce オブジェクト

[Power Query](/power-query/power-query-what-is-power-query) を利用

Salesforce Objects に接続し、Cognitive Search でインデックス作成のために検索可能なコンテンツを抽出します。

[[詳細]](search-how-to-index-power-query-data-sources.md)

:::column-end:::
:::column span="":::

---

### <a name="salesforce-reports"></a>Salesforce レポート

[Power Query](/power-query/power-query-what-is-power-query) を利用

Salesforce Reports に接続し、Cognitive Search でインデックス作成のために検索可能なコンテンツを抽出します。

[[詳細]](search-how-to-index-power-query-data-sources.md)

:::column-end:::
:::column span="":::

---

### <a name="smartsheet"></a>Smartsheet

[Power Query](/power-query/power-query-what-is-power-query) を利用

Smartsheet に接続し、Cognitive Search でインデックス作成のために検索可能なコンテンツを抽出します。

[[詳細]](search-how-to-index-power-query-data-sources.md)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="snowflake"></a>Snowflake

[Power Query](/power-query/power-query-what-is-power-query) を利用

Snowflake データベースから検索可能なデータとメタデータを抽出し、インデックスとデータ ソースの間のフィールド間マッピングに基づいてインデックスを設定します。 

[[詳細]](search-how-to-index-power-query-data-sources.md)

:::column-end:::
:::column span="":::

---
':::column-end::': null
':::column span=""::': null
---

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

<a name="partners"></a>

## <a name="data-sources-from-our-partners"></a>パートナーからのデータ ソース

データ ソース コネクタは、サードパーティの Microsoft パートナーからも提供されています。 データ ソースを使用する前に、[利用規約に関する声明](search-data-sources-terms-of-use.md)を参照し、パートナーのライセンスと使用方法に関する説明を確認してください。

:::row:::
:::column span="":::

---

### <a name="aderant"></a>Aderant

[BA Insight](https://www.bainsight.com/) を利用

Aderant コネクタでは、ソース システムのセキュリティを重視し、フル クロールと増分クロールの両方を提供するため、ユーザーは常に最新の情報を利用できます。

[[詳細]](https://www.bainsight.com/connectors/aderant-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="adobe-aem"></a>Adobe AEM

[Accenture](https://www.accenture.com) を利用

会社が Adobe Experience Manager サーバーからコンテンツをクロールできるようにし、接続の調整と、期待される値またはパターンのフィルター処理を提供します。

[[詳細]](https://contentanalytics.digital.accenture.com/display/aspire40/AEM+Connector)

:::column-end:::
:::column span="":::

---

### <a name="adobe-aem"></a>Adobe AEM

[BA Insight](https://www.bainsight.com/) を利用

Adobe Experience Manager コネクタを使用すると、Adobe Experience Manager (AEM) プラットフォームによって管理されるコンテンツのインデックス作成が可能になり、フル クロールと増分クロールの両方がサポートされるため、インデックスの鮮度を維持できます。

[[詳細]](https://www.bainsight.com/connectors/adobe-em-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="adobe-aem"></a>Adobe AEM

[Raytion](https://www.raytion.com/contact) を利用

Adobe Active Experience Manager (AEM) からのコンテンツに確実にインデックスを付け、Azure Cognitive Search でインテリジェントに検索するための安全なエンタープライズ検索コネクタです。 Adobe AEM からのページ、添付ファイル、その他の生成されたドキュメントの種類に対して、ほぼリアルタイムで確実にインデックスを付けます。 このコネクタでは、Adobe AEM のアクセス許可モデル、その組み込みのユーザーとグループの管理、Active Directory またはその他のディレクトリ サービスに基づく AEM のインストールを完全にサポートしています。

[[詳細]](https://www.raytion.com/connectors/adobe-experience-manager-aem)

:::column-end:::
:::column span="":::

---

### <a name="alfresco"></a>Alfresco

[BA Insight](https://www.bainsight.com/) を利用

Alfresco コネクタは、すべてのコネクタを構築するために使用されるプラットフォームであり、エンタープライズ システムへの安全な接続を提供する BAI コネクタ フレームワーク上に構築されます。

[[詳細]](https://www.bainsight.com/connectors/alfresco-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="alfresco"></a>Alfresco

[Raytion](https://www.raytion.com/contact) を利用

Alfresco One からのコンテンツに確実にインデックスを付け、Azure Cognitive Search でインテリジェントに検索するための安全なエンタープライズ検索コネクタです。 Alfresco One からのファイル、フォルダー、ユーザー プロファイルに対して、ほぼリアルタイムで確実にインデックスを付けます。 このコネクタでは、Alfresco One のアクセス許可モデル、その組み込みのユーザーとグループの管理、Active Directory とその他のディレクトリ サービスに基づく Alfresco One のインストールを完全にサポートしています。

[[詳細]](https://www.raytion.com/connectors/raytion-alfresco-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="amazon-aurora"></a>Amazon Aurora

[BA Insight](https://www.bainsight.com/) を利用

Amazon Aurora コネクタは、業界標準のデータベース アクセス方法に基づいて構築されているので、Oracle、MySQL、IBM DB2 などの他のシステムのデータベースも同様にサポートします。

[[詳細]](https://www.bainsight.com/connectors/amazon-aurora-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="amazon-rds"></a>Amazon RDS

[BA Insight](https://www.bainsight.com/) を利用

Amazon RDS コネクタは、業界標準のデータベース アクセス方法に基づいて構築されているので、Oracle、MySQL、IBM DB2 などの他のシステムのデータベースも同様にサポートできます。

[[詳細]](https://www.bainsight.com/connectors/amazon-rds-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="amazon-s3"></a>Amazon S3

[BA Insight](https://www.bainsight.com/) を利用

Amazon S3 コネクタは、S3 に格納されているすべてのコンテンツと連携します。 組織は、このコネクタを使用して S3 に安全に接続し、S3 バケットからコンテンツにインデックスを付けることができます。 強力なフィルター処理機能を使用すると、S3 で検索されたどのコンテンツにインデックスを付けるかを組織が制御できます。

[[詳細]](https://www.bainsight.com/connectors/amazon-s3-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="aspider"></a>Aspider

[Accenture](https://www.accenture.com) を利用

Aspider コネクタを使用すると、HTTP 認証、増分クロール、接続の調整、分散および HTTPS クロールなどの機能を使用して、Web サイトからのコンテンツのクロールを行うことができます。

[[詳細]](https://contentanalytics.digital.accenture.com/display/aspire40/Aspider+Web+Crawler)

:::column-end:::
:::column span="":::

---

### <a name="atlassian-confluence-cloud"></a>Atlassian Confluence (クラウド)

[BA Insight](https://www.bainsight.com/) を利用

Confluence (クラウド バージョン) コネクタは、Confluence に格納されているコンテンツのクロールとインデックス作成を可能にするエンタープライズ グレードのインデックス コネクタです。

[[詳細]](https://www.bainsight.com/connectors/connector-for-confluence-cloud-version/)

:::column-end:::
:::column span="":::

---

### <a name="azure-ad"></a>Azure AD

[BA Insight](https://www.bainsight.com) を利用

BA Insight Azure Active Directory コネクタを使用すると、Azure Active Directory テナンシーのコンテンツを他のリポジトリのコンテンツと共に 1 つの統合された検索インデックスに表示できるため、従業員の検索や専門知識のロケーターなどの検索が可能になります。

[[詳細]](https://www.bainsight.com/connectors/azure-active-directory-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="azure-ad"></a>Azure AD

[Raytion](https://www.raytion.com/contact) を利用

Microsoft Azure Active Directory (Azure AD) からのコンテンツに確実にインデックスを付け、Azure Cognitive Search でインテリジェントに検索するための安全なエンタープライズ検索コネクタです。 Microsoft Graph API を使用して Azure AD のオブジェクトにインデックスを付けます。 このコネクタを使用して、プリンシパルをほぼリアルタイムで Cognitive Search に取り込み、専門家の検索、機器の検索、場所の検索などのユース ケースを実装したり、カスタム データ ソースと組み合わせて事前バインディングのセキュリティによるトリミングを提供したりできます。 このコネクタは、Microsoft 365 に対するフェデレーション認証をサポートしています。

[[詳細]](https://www.raytion.com/connectors/raytion-azure-ad-connector)

:::column-end:::
:::column span="":::

---

### <a name="azure-blobs"></a>Azure BLOB

[Accenture](https://www.accenture.com) を利用

Azure Blob コンテナーからコンテンツをクロールする機能を提供します。これにより、増分クロール、ドキュメント レベルのセキュリティ、フォルダーとサブフォルダーへのアクセスが可能になります。

[[詳細]](https://contentanalytics.digital.accenture.com/display/aspire40/Azure+Blob+Storage+Connector)

:::column-end:::
:::column span="":::

---

### <a name="azure-data-lake"></a>Azure Data Lake

[Accenture](https://www.accenture.com) を利用

Azure Data Lake コネクタは、増分クロール、オブジェクト ACL の取得、OAuth 2 認証などを使用して、ルートまたは指定されたパスで Azure Data Lake Store クラウドからコンテンツをクロールします。

[[詳細]](https://contentanalytics.digital.accenture.com/display/aspire40/Azure+Data+Lake+Connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="azure-events-hub"></a>Azure イベント ハブ

[Accenture](https://www.accenture.com) を利用

Azure イベント ハブからのコンテンツをクロールします。これにより、任意の種類のイベントや属性の増分クロールおよび取得が可能になります。

[[詳細]](https://contentanalytics.digital.accenture.com/display/aspire40/Azure+Events+Hub+Connector)

:::column-end:::
:::column span="":::

---

### <a name="azure-sql-database"></a>Azure SQL Database

[BA Insight](https://www.bainsight.com/) を利用

BA Insight の Azure SQL Database コネクタでは、ソース データベースのセキュリティを重視し、フル クロールと増分クロールの両方を提供するため、ユーザーは常に最新の情報を利用できます。

[[詳細]](https://www.bainsight.com/connectors/azure-sql-database-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="bentley"></a>Bentley

[BA Insight](https://www.bainsight.com/) を利用

BAI Bentley AssetWise コネクタを使用すると、AssetWise からのコンテンツを他のリポジトリのコンテンツと共に 1 つの統合された検索インデックスに表示できます。

[[詳細]](https://www.bainsight.com/connectors/bentley-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="box"></a>ボックス

[Accenture](https://www.accenture.com) を利用

Box コネクタでは、Box リポジトリからのコンテンツをクロールします。 このコネクタでは、REST API を使用して、サポートされている要素を取得し、フル クロールや増分クロール、メタデータの抽出、ACL の取得などを提供します。

[[詳細]](https://contentanalytics.digital.accenture.com/display/aspire40/Box++Connector)

:::column-end:::
:::column span="":::

---

### <a name="box"></a>ボックス

[BA Insight](https://www.bainsight.com/) を利用

Box コネクタを使用すると、SharePoint およびその他のポータルの Box からのコンテンツを表示できるようになるため、ユーザーは SharePoint と Box から統合された検索結果を得ることができます。

[[詳細]](https://www.bainsight.com/connectors/box-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="box"></a>ボックス

[Raytion](https://www.raytion.com/contact) を利用

Box からのコンテンツに確実にインデックスを付け、Azure Cognitive Search でインテリジェントに検索するための安全なエンタープライズ検索コネクタです。 Box からのファイル、フォルダー、コメント、ユーザー、グループ、タスクに対して、ほぼリアルタイムで確実にインデックスを付けます。 このコネクタでは、Box の組み込みのユーザーとグループの管理を完全にサポートしています。

[[詳細]](https://www.raytion.com/connectors/raytion-box-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="confluence"></a>Confluence

[Accenture](https://www.accenture.com) を利用

Confluence コネクタでは、Confluence コンテンツ リポジトリからのコンテンツをクロールします。 このコネクタでは、REST API 経由でスペース、ページ、ブログ、添付ファイル、コメントを取得するため、増分クロール、ACL の取得、HTTP と HTTPS のサポートなどが可能になります。

[[詳細]](https://contentanalytics.digital.accenture.com/display/aspire40/Atlassian+Confluence+Connector)

:::column-end:::
:::column span="":::

---

### <a name="confluence"></a>Confluence

[BA Insight](https://www.bainsight.com/) を利用

Confluence コネクタは、Confluence に格納されているコンテンツのクロールとインデックス作成を可能にするエンタープライズ グレードのインデックス コネクタです。 これにより、SharePoint またはその他のポータルは、ユーザーが複数のコンテンツ ソースから必要なコンテンツを検索および取得できる単一のポイントとして機能できるようになります。

[[詳細]](https://www.bainsight.com/connectors/confluence-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="confluence"></a>Confluence

[Raytion](https://www.raytion.com/contact) を利用

Atlassian Confluence からのコンテンツに確実にインデックスを付け、Azure Cognitive Search でインテリジェントに検索するための安全なエンタープライズ検索コネクタです。 オンプレミスの Confluence インスタンスからのページ、ブログ投稿、添付ファイル、コメント、スペース、プロファイル、タグ用のハブ サイトに対して、ほぼリアルタイムで確実にインデックスを付けます。 このコネクタでは、Atlassian Confluence の組み込みのユーザーとグループの管理、Active Directory とその他のディレクトリ サービスに基づく Confluence のインストールを完全にサポートしています。

[[詳細]](https://www.raytion.com/connectors/raytion-confluence-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="confluence-cloud"></a>Confluence Cloud

[Raytion](https://www.raytion.com/contact) を利用

Atlassian Confluence Cloud からのコンテンツに確実にインデックスを付け、Azure Cognitive Search でインテリジェントに検索するための安全なエンタープライズ検索コネクタです。 Confluence Cloud インスタンスからのページ、ブログ投稿、添付ファイル、コメント、スペース、プロファイル、タグ用のハブ サイトに対して、ほぼリアルタイムで確実にインデックスを付けます。 このコネクタでは、Atlassian Confluence Cloud の組み込みのユーザーとグループの管理を完全にサポートしています。

[[詳細]](https://www.raytion.com/connectors/raytion-confluence-cloud-connector)

:::column-end:::
:::column span="":::

---

### <a name="cuadrastar"></a>CuadraSTAR

[BA Insight](https://www.bainsight.com/) を利用

CuadraSTAR コネクタでは、CuadraSTAR のコンテンツをクロールし、単一のインデックスを作成します。これにより、Azure Cognitive Search を使用して CuadraSTAR および 70 を超える他のサポートされているリポジトリ内の関連情報を検索できるようになるため、個別の検索を実行する必要がなくなります。

[[詳細]](https://www.bainsight.com/connectors/cuadrastar-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="database"></a>データベース

[Accenture](https://www.accenture.com) を利用

データベース サーバー コネクタでは、リレーショナル データベース サーバーからのコンテンツをクロールし、サーバー上のすべてのデータベースをスキャンして、行とテーブルの情報を抽出します。

[[詳細]](https://contentanalytics.digital.accenture.com/display/aspire40/Database+Server+Connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="deltek"></a>Deltek

[BA Insight](https://www.bainsight.com/) を利用

Deltek Vision コネクタでは、ソース システムのセキュリティを重視し、フル クロールと増分クロールの両方を提供するため、ユーザーは常に最新の情報を利用できます。 Deltek Vision からのコンテンツを Azure、SharePoint Online、または SharePoint 2016/2013 にインデックス付けして、BA Insight の SmartHub を介して表示し、統合された検索結果をユーザーに提供します。

[[詳細]](https://www.bainsight.com/connectors/deltek-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="documentum"></a>Documentum

[Accenture](https://www.accenture.com) を利用

Aspire Documentum DQL コネクタでは、Documentum からのコンテンツをクロールします。これにより、ユーザー定義の DQL SELECT ステートメント、増分クロール、ACL の取得、入れ子になったアクセス許可のグループ拡張などに基づいたクロールが可能になります。

[[詳細]](https://contentanalytics.digital.accenture.com/display/aspire40/Documentum+DQL+Connector)

:::column-end:::
:::column span="":::

---

### <a name="documentum"></a>Documentum

[BA Insight](https://www.bainsight.com/) を利用

BA Insight の Documentum コネクタでは、Documentum オブジェクトのフル テキストとメタデータの両方を Azure Cognitive Search に安全にインデックス付けすることで、複数のリポジトリのコンテンツ全体で 1 つの検索可能な結果セットを有効にします。 これは、プロセス管理のために一度に 1 件ずつ Azure Cognitive Search で Documentum レコードを表示する他の一部のコネクタとは異なります。

[[詳細]](https://www.bainsight.com/connectors/documentum-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="documentum"></a>Documentum

[Raytion](https://www.raytion.com/contact) を利用

OpenText Documentum からのコンテンツに確実にインデックスを付け、Azure Cognitive Search でインテリジェントに検索するための安全なエンタープライズ検索コネクタです。 Documentum からのメタデータとプロパティと共に、リポジトリ、フォルダー、ファイルに対して、ほぼリアルタイムで確実にインデックスを付けます。 このコネクタでは、OpenText Documentum の組み込みのユーザーとグループの管理を完全にサポートしています。

[[詳細]](https://www.raytion.com/connectors/raytion-documentum-connector)

:::column-end:::
:::column span="":::

---

### <a name="egnyte"></a>Egnyte

[BA Insight](https://www.bainsight.com/) を利用

Egnyte コネクタでは、フル クロールと増分クロールの両方とインデックスを、非常に高いスループットでサポートしています。

[[詳細]](https://www.bainsight.com/connectors/egnyte-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="elasticsearch"></a>Elasticsearch

[Accenture](https://www.accenture.com) を利用

Elasticsearch コネクタでは、Elasticsearch インデックスからのコンテンツをクロールし、複数のインデックスのクロール、Slice のサポート、コンテンツを取得するための MGet メソッドの Get の使用、接続の調整を可能にします。

[[詳細]](https://contentanalytics.digital.accenture.com/display/aspire40/Elasticsearch+Connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="elite--e3"></a>Elite/E3

[BA Insight](https://www.bainsight.com/) を利用

BA Insight の Elite コネクタでは、弁護士が Azure Cognitive Search を使用して Elite のコンテンツに基づいて企業のコンテンツや知識にアクセスするための単一のアクセス ポイントを提供します。

[[詳細]](https://www.bainsight.com/connectors/elite-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="emc-eroom"></a>EMC eRoom

[BA Insight](https://www.bainsight.com/) を利用

eRoom コネクタでは、eRoom アプリケーションへの安全な接続を確立し、メタデータや添付ファイルなどのコンテンツを eRoom スキーマから検索エンジン スキーマにマップします。 その後、コンテンツを抽出し、クロールと呼ばれるプロセスで検索エンジンにフィードします。

[[詳細]](https://www.bainsight.com/connectors/eroom-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="facebook-workplace"></a>Facebook Workplace

[BA Insight](https://www.bainsight.com/) を利用

Workplace by Facebook を使用している組織は、BA Insight Workplace by Facebook コネクタを使用して、このデータのリーチを既存の検索インデックスに拡張できるようになりました。

[[詳細]](https://www.bainsight.com/connectors/connector-for-workplace-by-facebook/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="facebook-workplace"></a>Facebook Workplace

[Raytion](https://www.raytion.com/contact) を利用

Facebook Workplace からのコンテンツに確実にインデックスを付け、Azure Cognitive Search でインテリジェントに検索するための安全なエンタープライズ検索コネクタです。 Facebook Workplace からのプロジェクト グループ、会話、共有ドキュメントに対して、ほぼリアルタイムで確実にインデックスを付けます。 このコネクタでは、Facebook Workplace の組み込みのユーザーとグループの管理を完全にサポートしています。

[[詳細]](https://www.raytion.com/connectors/raytion-facebook-workplace-connector)

:::column-end:::
:::column span="":::
---

### <a name="file-share"></a>ファイル共有

[BA Insight](https://www.bainsight.com/) を利用

ファイル共有コネクタを使用すると、1 つの統合された検索インデックス内のファイル共有 (Windows、SMB/CIFS) のコンテンツと、他のリポジトリのコンテンツを表示することができます。

[[詳細]](https://www.bainsight.com/connectors/file-share-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="file-system"></a>ファイル システム

[Accenture](https://www.accenture.com) を利用

ファイル システム コネクタは、ファイル システムの場所からのコンテンツをクロールします。これにより、増分クロール、メタデータの抽出、パスによるドキュメントのフィルター処理、Windows、Linux、MacOS ファイル システムのサポートが可能になります。

[[詳細]](https://contentanalytics.digital.accenture.com/display/aspire40/File+System+Connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::
---

### <a name="file-system"></a>ファイル システム

[Raytion](https://www.raytion.com/contact) を利用

ローカルにマウントされたファイル システムからのコンテンツに確実にインデックスを付け、Azure Cognitive Search でインテリジェントに検索するための安全なエンタープライズ検索コネクタです。 ファイル システムからのファイルとフォルダーに対して、ほぼリアルタイムで確実にインデックスを付けます。

[[詳細]](https://www.raytion.com/connectors/raytion-file-system-connector)

:::column-end:::
:::column span="":::

---

### <a name="firstspirit"></a>FirstSpirit

[Raytion](https://www.raytion.com/contact) を利用

e-Spirit FirstSpirit からのコンテンツに確実にインデックスを付け、Azure Cognitive Search でインテリジェントに検索するための安全なエンタープライズ検索コネクタです。 FirstSpirit からのページ、添付ファイル、その他の生成されたドキュメントの種類に対して、ほぼリアルタイムで確実にインデックスを付けます。 このコネクタでは、e-Spirit FirstSpirit の組み込みのユーザー、グループ、アクセス許可の管理、Active Directory およびその他のディレクトリ サービスに基づく FirstSpirit インストールを完全にサポートしています。

[[詳細]](https://www.raytion.com/connectors/raytion-firstspirit-connector)

:::column-end:::
:::column span="":::

---

### <a name="gitlab"></a>GitLab

[Raytion](https://www.raytion.com/contact) を利用

GitLab からのコンテンツに確実にインデックスを付け、Azure Cognitive Search でインテリジェントに検索するための安全なエンタープライズ検索コネクタです。 GitLab からのプロジェクト、ファイル、フォルダー、コミット メッセージ、問題、Wiki ページに対して、ほぼリアルタイムで確実にインデックスを付けます。 このコネクタでは、GitLab の組み込みのユーザーとグループの管理を完全にサポートしています。

[[詳細]](https://www.raytion.com/connectors/raytion-gitlab-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="google-cloud-sql"></a>Google Cloud SQL

[BA Insight](https://www.bainsight.com/) を利用

Google Cloud SQL コネクタでは、Google Cloud SQL からのコンテンツを Azure Cognitive Search インデックスにインデックス付けし、BA Insight の SmartHub を介して表示し、統合された検索結果をユーザーに提供します。

[[詳細]](https://www.bainsight.com/connectors/google-cloud-sql-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="google-drive"></a>Google ドライブ

[BA Insight](https://www.bainsight.com/) を利用

BAI Google ドライブ コネクタを使用すると、Google ドライブのコンテンツを参照する 1 つの統合された検索インデックス内の Google ドライブのコンテンツと、他のリポジトリのコンテンツを表示することができます。

[[詳細]](https://www.bainsight.com/connectors/google-drive-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="google-drive"></a>Google ドライブ

[Raytion](https://www.raytion.com/contact) を利用

Google ドライブからのコンテンツに確実にインデックスを付け、Azure Cognitive Search でインテリジェントに検索するための安全なエンタープライズ検索コネクタです。 Google ドライブから個人用ドライブおよびチーム ドライブ上のファイル、フォルダー、コメントに対して、ほぼリアルタイムで確実にインデックスを付けます。 このコネクタは、Google ドライブの組み込みのアクセス許可モデルと、Google Admin Directory によるユーザーとグループの管理を完全にサポートしています。

[[詳細]](https://www.raytion.com/connectors/raytion-google-drive-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="hp-consolidated-archive-eas"></a>HP Consolidated Archive (EAS)

[BA Insight](https://www.bainsight.com/) を利用

BA Insight の HP Consolidated Archive コネクタでは、アーカイブ内のドキュメントのフル テキストとメタデータの両方を、SharePoint Search や Azure Search などのさまざまな検索エンジンに安全にインデックス付けします。 これにより、複数のリポジトリのコンテンツ全体で 1 つの検索可能な結果セットが有効になります。 これにより、組織は、Consolidated Archive、SharePoint、その他のリポジトリ内でアクセスできる豊富な情報を活用して、検索を通じてユーザーにすぐにデータを提供できるようになります。

[[詳細]](https://www.bainsight.com/connectors/hp-consolidated-archive-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="ibm-connections"></a>IBM Connections

[Accenture](https://www.accenture.com) を利用

IBM Connections コネクタでは IBM Connections サーバーからのコンテンツをクロールし、増分クロール、メタデータの抽出、ACL の取得、正規表現パターンによるドキュメントのフィルター処理などを行います。

[[詳細]](https://contentanalytics.digital.accenture.com/display/aspire40/IBM+Connections+Connector)

:::column-end:::
:::column span="":::

---

### <a name="ibm-connections"></a>IBM Connections

[BA Insight](https://www.bainsight.com/) を利用

IBM Connections コネクタは IBM Connections 用に開発されており、Connections アプリケーションへの安全な接続を確立し、メタデータや添付ファイルなどのコンテンツを Connections スキーマから検索エンジン スキーマにマップします。 その後、コンテンツを抽出し、クロールと呼ばれるプロセスで検索エンジンにフィードします。

[[詳細]](https://www.bainsight.com/connectors/ibm-connections-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="ibm-connections"></a>IBM Connections

[Raytion](https://www.raytion.com/contact) を利用

IBM Connections からのコンテンツに確実にインデックスを付け、Azure Cognitive Search でインテリジェントに検索するための安全なエンタープライズ検索コネクタです。 オンプレミスの Connections インスタンスからのパブリックおよび個人用ファイル、ブログ、Wiki、フォーラム、コミュニティ、ブックマーク、プロファイル、ステータスの更新に対して、ほぼリアルタイムで確実にインデックスを付けます。 このコネクタでは、IBM Connection の組み込みのユーザーとグループの管理、Active Directory とその他のディレクトリ サービスに基づく Connections のインストールを完全にサポートしています。

[[詳細]](https://www.raytion.com/connectors/raytion-ibm-connections-connector)

:::column-end:::
:::column span="":::

---

### <a name="ibm-connections-cloud"></a>IBM Connections Cloud

[Raytion](https://www.raytion.com/contact) を利用

IBM Connections Cloud からのコンテンツに確実にインデックスを付け、Azure Cognitive Search でインテリジェントに検索するための安全なエンタープライズ検索コネクタです。 Connections Cloud からのパブリックおよび個人用ファイル、ブログ、Wiki、フォーラム、コミュニティ、ブックマーク、プロファイル、ステータスの更新に対して、ほぼリアルタイムで確実にインデックスを付けます。 このコネクタでは、IBM Connections Cloud の組み込みのユーザーとグループの管理を完全にサポートしています。

[[詳細]](https://www.raytion.com/connectors/raytion-ibm-connections-cloud-connector)

:::column-end:::
:::column span="":::

---

### <a name="ibm-content-manager"></a>IBM Content Manager

[BA Insight](https://www.bainsight.com/) を利用

IBM Content Manager コネクタでは、ソース アプリケーションのセキュリティを重視し、フル クロールと増分クロールの両方を提供するため、ユーザーは常に最新の情報を利用できます。

[[詳細]](https://www.bainsight.com/connectors/ibm-content-manager-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="ibm-db2"></a>IBM Db2

[BA Insight](https://www.bainsight.com/) を利用

Db2 コネクタを使用すると、組織は DB2 データベースやアプリケーション内に格納されている豊富なデータを活用して、検索を通じてユーザーにすぐにデータを提供できるようになります。

[[詳細]](https://www.bainsight.com/connectors/ibm-db2-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="ibm-filenet-p8"></a>IBM FileNet P8

[BA Insight](https://www.bainsight.com/) を利用

IBM FileNet Content Manager コネクタを使用すると、SharePoint およびその他のポータルのユーザーは、FileNet リポジトリに格納されているコンテンツを安全に検索できます。 コンテンツへのアクセスは、FileNet で確立されたセキュリティによって決定され、他のポータルを介してアクセスした場合も、直接 FileNet 内にあるのと同じくらい安全であることを保証します。

[[詳細]](https://www.bainsight.com/connectors/ibm-filenet-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="ibm-lotus-notes"></a>IBM Lotus Notes

[BA Insight](https://www.bainsight.com/) を利用

BA Insight の IBM Notes Email コネクタを使用すると、ユーザーは SharePoint または別のポータル内から Lotus Notes の電子メールを直接検索することができます。 IBM Notes 内で定義されているセキュリティは検索エクスペリエンスに自動的に反映されるため、ユーザーは自分のメールボックス、パブリック メールボックス、アクセス権が付与されているその他のメールボックスから検索結果を表示できます。

[[詳細]](https://www.bainsight.com/connectors/lotus-notes-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="ibm-websphere"></a>IBM WebSphere

[BA Insight](https://www.bainsight.com/) を利用

BA Insight の WebSphere コネクタでは、WebSphere オブジェクトのフル テキストとメタデータの両方を Microsoft の検索エンジンに安全にインデックス付けすることで、複数のリポジトリのコンテンツ全体で 1 つの検索可能な結果セットを有効にします。 これにより、組織は、Microsoft プラットフォーム内でアクセス可能な豊富な情報を活用して、検索を通じてユーザーにすぐにデータを提供できるようになります。

[[詳細]](https://www.bainsight.com/connectors/ibm-websphere-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="imanage-cloud"></a>iManage Cloud

[BA Insight](https://www.bainsight.com/) を利用

BA Insight のiManage Cloud コネクタでは、Work ワークスペース内のドキュメントのフル テキストとメタデータの両方を検索エンジンに安全にインデックス付けします。

[[詳細]](https://www.bainsight.com/connectors/connector-for-imanage-work-cloud/)

:::column-end:::
:::column span="":::

---

### <a name="imanage-work"></a>iManage Work

[BA Insight](https://www.bainsight.com/) を利用

iManage Work コネクタでは、完全なセキュリティを提供し、高スループットで動作して、Work でのパフォーマンスの影響を低く保ちながら、クロール時間を最小限に抑えます。 読み取りアクセスだけが必要であり、iManage サーバーにクライアント ソフトウェアをインストールする必要はありません。 その結果、iManage Work に保存されているすべてのコンテンツにシームレスかつ同時にアクセスできます。

[[詳細]](https://www.bainsight.com/connectors/imanage-work-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="jira"></a>Jira

[BA Insight](https://www.bainsight.com/) を利用

Jira コネクタを使用すると、ユーザーはすべての Jira オブジェクトに対して検索を実行できるため、Jira に直接アクセスする必要がなくなります。

[[詳細]](https://www.bainsight.com/connectors/jira-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="jira"></a>Jira

[Raytion](https://www.raytion.com/contact) を利用

Atlassian Jira からのコンテンツに確実にインデックスを付け、Azure Cognitive Search でインテリジェントに検索するための安全なエンタープライズ検索コネクタです。 オンプレミスの Jira インスタンスからのプロジェクト、問題、添付ファイル、コメント、作業ログ、問題の履歴、リンク、プロファイルに対して、ほぼリアルタイムで確実にインデックスを付けます。 このコネクタでは、Atlassian Jira の組み込みのユーザーとグループの管理、Active Directory とその他のディレクトリ サービスに基づく Jira のインストールを完全にサポートしています。

[[詳細]](https://www.raytion.com/connectors/raytion-jira-connector)

:::column-end:::
:::column span="":::

---

### <a name="jira-cloud"></a>Jira Cloud

[BA Insight](https://www.bainsight.com/) を利用

Jira (クラウド バージョン) コネクタでは、すべての Jira オブジェクトに対して検索を実行するため、Jira に直接アクセスする必要がなくなります。

[[詳細]](https://www.bainsight.com/connectors/jira-cloud-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="jira-cloud"></a>Jira Cloud

[Raytion](https://www.raytion.com/contact) を利用

Atlassian Jira Cloud からのコンテンツに確実にインデックスを付け、Azure Cognitive Search でインテリジェントに検索するための安全なエンタープライズ検索コネクタです。 Jira Cloud からのプロジェクト、問題、添付ファイル、コメント、作業ログ、問題の履歴、リンク、プロファイルに対して、ほぼリアルタイムで確実にインデックスを付けます。 このコネクタでは、Atlassian Jira Cloud の組み込みのユーザーとグループの管理を完全にサポートしています。

[[詳細]](https://www.raytion.com/connectors/raytion-jira-cloud-connector)

:::column-end:::
:::column span="":::

---

### <a name="jive"></a>Jive

[BA Insight](https://www.bainsight.com/) を利用

Jive コネクタは Jive 用に開発されており、Jive アプリケーションへの安全な接続を確立し、メタデータや添付ファイルなどのコンテンツを Jive スキーマから検索エンジン スキーマにマップします。 その後、コンテンツを抽出し、クロールと呼ばれるプロセスで検索エンジンにフィードします。

[[詳細]](https://www.bainsight.com/connectors/jive-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="jive"></a>Jive

[Raytion](https://www.raytion.com/contact) を利用

Jive からのコンテンツに確実にインデックスを付け、Azure Cognitive Search でインテリジェントに検索するための安全なエンタープライズ検索コネクタです。 オンプレミスとクラウドでホストされる Jive インスタンスからのディスカッション、投票、ファイル、ブログ、スペース、グループ、プロジェクト、タスク、ビデオ、メッセージ、アイデア、プロファイル、ステータスの更新に対して、ほぼリアルタイムで確実にインデックスを付けます。 このコネクタでは、Jive の組み込みのユーザーとグループの管理を完全にサポートし、Jive のネイティブ認証モデル、OAuth、Basic 認証をサポートしています。

[[詳細]](https://www.raytion.com/connectors/raytion-jive-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="kaltura"></a>Kaltura

[BA Insight](https://www.bainsight.com/) を利用

Kaltura コネクタを使用すると、ビデオだけでなく、カテゴリ、データ、ドキュメントなど、さまざまな種類の情報のインデックス作成を行うことができます。

[[詳細]](https://www.bainsight.com/connectors/kaltura-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="ldap"></a>LDAP

[BA Insight](https://www.bainsight.com/) を利用 

LDAP コネクタを使用すると、組織は LDAP に準拠している任意のディレクトリに接続し、そのディレクトリの任意のレコードにインデックスを付けることができます。 組織では、ディレクトリの特定のサブセットにフィルター処理を適用し、特定のフィールドのみを取得できます。これにより、ディレクトリ内の任意の場所に格納されているユーザー、連絡先、またはグループを簡単に検索できます。

[[詳細]](https://www.bainsight.com/connectors/ldap-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="ldap"></a>LDAP

[Raytion](https://www.raytion.com/contact) を利用

ライトウェイト ディレクトリ アクセス プロトコル (LDAP) と互換性のあるディレクトリ サービスからコンテンツに確実にインデックスを付け、Azure Cognitive Search でインテリジェントに検索するための安全なエンタープライズ検索コネクタです。 Active Directory、Novell E-Directory、その他の LDAP 互換ディレクトリ サービスからの LDAP オブジェクトに対して、ほぼリアルタイムで確実にインデックスを付けます。 このコネクタを使用して、専門家、機器、場所の検索などのユース ケースや、カスタム データ ソースのセキュリティによるトリミングの実装のために、プリンシパルを Google Cloud Search に取り込むことができます。 このコネクタでは、SSL 経由の LDAP がサポートされます。

[[詳細]](https://www.raytion.com/connectors/raytion-ldap-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="legalkey"></a>LegalKEY

[BA Insight](https://www.bainsight.com/) を利用 

BA Insight の OpenText LegalKEY コネクタでは、LegalKEY のクライアントおよび問題レコードのフル テキストとメタデータの両方を、Microsoft の検索エンジンに安全にインデックス付けすることで、複数のリポジトリのコンテンツ全体で 1 つの検索可能な結果セットを有効にします。 これにより、組織は、LegalKEY、SharePoint、その他のリポジトリ内でアクセスできる豊富な情報を活用して、検索を通じてユーザーにすぐにデータを提供できるようになります。

[[詳細]](https://www.bainsight.com/connectors/legalkey-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="lexisnexis-interaction"></a>LexisNexis InterAction

[BA Insight](https://www.bainsight.com/) を利用

LexisNexis InterAction コネクタを使用すると、組織全体の弁護士や他の会社の従業員が、直接ログインして個別の検索を実行しなくても、InterAction に格納されている重要な情報を簡単に見つけることができます。

[[詳細]](https://www.bainsight.com/connectors/lexisnexis-interaction-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="lotus-notes-databases"></a>Lotus Notes データベース

[BA Insight](https://www.bainsight.com/) を利用 

IBM Notes Database コネクタを使用すると、ユーザーは、Azure Cognitive Search を使用して Notes データベースに格納されているコンテンツを検索することができます。 IBM Notes 内で定義されたセキュリティは検索エクスペリエンスに自動的に反映されるため、ユーザーは認可されたコンテンツを確実に参照できます。 最終的に、ユーザーは必要なものすべてを 1 か所で見つけることができます。

[[詳細]](https://www.bainsight.com/connectors/ibm-lotus-notes-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="m-files"></a>M-Files

[BA Insight](https://www.bainsight.com/) を利用

M-Files コネクタでは、M-Files プラットフォームによって管理されるコンテンツのインデックス作成を可能にし、フル クロールと増分クロールの両方をサポートしてインデックスの鮮度を維持します。

[[詳細]](https://www.bainsight.com/connectors/m-files-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="mediaplatform-primetime"></a>MediaPlatform PrimeTime

[BA Insight](https://www.bainsight.com/) を利用

BA Insight の MediaPlatform PrimeTime インデックス作成コネクタを使用すると、ユーザーは、コネクタと BA Insight の SmartHub を組み合わせて、組織のエンタープライズ検索プラットフォームを介してコンテンツにアクセスできるようになります。 BA Insight MediaPlatform PrimeTime コネクタでは、MediaPlatform PrimeTime からチャネルとビデオに関する情報を取得し、Azure Cognitive Search を介してインデックスを付けます。

[[詳細]](https://www.bainsight.com/connectors/mediaplatform-primetime-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="mediawiki"></a>MediaWiki

[Raytion](https://www.raytion.com/contact) を利用

MediaWiki からのコンテンツに確実にインデックスを付け、Azure Cognitive Search でインテリジェントに検索するための安全なエンタープライズ検索コネクタです。 MediaWiki インスタンスからのページ、ディスカッション ページ、添付ファイルに対して、ほぼリアルタイムで確実にインデックスを付けます。 このコネクタでは、MediaWiki の組み込みのアクセス許可モデルと、Active Directory や他のディレクトリ サービスに基づく MediaWiki インストールを完全にサポートしています。

[[詳細]](https://www.raytion.com/connectors/raytion-mediawiki-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="micro-focus-content-manager-hpe-records-managerhp-trim"></a>Micro Focus Content Manager (HPE Records Manager/HP TRIM)

[BA Insight](https://www.bainsight.com/) を利用

HP TRIM コネクタは HP Records Manager 用に開発されており、TRIM アプリケーションへの安全な接続を確立し、メタデータや添付ファイルなどのコンテンツを TRIM スキーマから検索エンジン スキーマにマップします。 その後、コンテンツを抽出し、クロールと呼ばれるプロセスで検索エンジンにフィードします。

[[詳細]](https://www.bainsight.com/connectors/hp-trim-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="microsoft-dynamics-365"></a>Microsoft Dynamics 365

[BA Insight](https://www.bainsight.com/) を利用

Microsoft Dynamics 365 CRM コネクタでは、オンプレミスの CRM のインストールと Dynamics CRM Online の両方をサポートしています。

[[詳細]](https://www.bainsight.com/connectors/microsoft-dynamics-crm-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="microsoft-dynamics-365-cloud"></a>Microsoft Dynamics 365 (Cloud)

[BA Insight](https://www.bainsight.com/) を利用

Microsoft Dynamics 365 (クラウド バージョン) CRM コネクタでは、CRM アプリケーションへの安全な接続を確立し、コンテンツを CRM スキーマから検索エンジン スキーマにマップします。

[[詳細]](https://www.bainsight.com/connectors/connector-for-microsoft-dynamics-cloud/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="microsoft-exchange-online"></a>Microsoft Exchange Online

[BA Insight](https://www.bainsight.com/) を利用

BA Insight Microsoft Exchange Online コネクタを使用すると、ユーザーはさまざまな検索プラットフォームを介して Exchange Online からコンテンツを取得できます。

[[詳細]](https://www.bainsight.com/connectors/microsoft-exchange-online-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="microsoft-exchange-public-folders"></a>Microsoft Exchange パブリック フォルダー

[BA Insight](https://www.bainsight.com/) を利用

BAI Microsoft Exchange パブリック フォルダー コネクタを使用すると、ユーザーはさまざまな検索プラットフォームを介して Exchange からコンテンツを取得できます。

[[詳細]](https://www.bainsight.com/connectors/microsoft-exchange-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="microsoft-exchange-server"></a>Microsoft Exchange Server

[BA Insight](https://www.bainsight.com/) を利用

BA Insight Microsoft Exchange コネクタを使用すると、ユーザーはさまざまな検索エンジンを介して Exchange からコンテンツを取得できます。

[[詳細]](https://www.bainsight.com/connectors/microsoft-exchange-server-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="microsoft-sql-server"></a>Microsoft SQL Server

[BA Insight](https://www.bainsight.com/) を利用

このデータベース コネクタは、業界標準のデータベース アクセス方法に基づいて構築されているので、Oracle、MySQL、IBM DB2 などの他のシステムのデータベースも同様にサポートできます。 これは、ソース データベースのセキュリティを重視し、フル クロールと増分クロールの両方を提供するため、ユーザーは常に最新の情報を利用できます。

[[詳細]](https://www.bainsight.com/connectors/sql-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="microsoft-teams"></a>Microsoft Teams

[BA Insight](https://www.bainsight.com/) を利用

BA Insight Microsoft Teams コネクタでは、Microsoft Teams からのコンテンツを他のエンタープライズ システムからのコンテンツと一緒にインデックス付けして、統合された結果を提供します。

[[詳細]](https://www.bainsight.com/connectors/microsoft-teams-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="microsoft-windows-file-server"></a>Microsoft Windows File Server

[Raytion](https://www.raytion.com/contact) を利用

分散ファイル システム (DFS) を含む Microsoft Windows File Server からのコンテンツに確実にインデックスを付け、Azure Cognitive Search でインテリジェントに検索するための安全なエンタープライズ検索コネクタです。 Windows File Server からのファイルとフォルダーに対して、ほぼリアルタイムで確実にインデックスを付けます。 このコネクタでは、Microsoft Windows File Server のドキュメント レベルのセキュリティと、SMB2 および SMB3 プロトコルの最新バージョンを完全にサポートしています。

[[詳細]](https://www.raytion.com/connectors/raytion-windows-file-server-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="mysql"></a>MySQL

[BA Insight](https://www.bainsight.com/) を利用

MySQL コネクタは、業界標準のデータベース アクセス方法に基づいて構築されているので、Oracle、MySQL、IBM DB2 などの他のシステムのデータベースも同様にサポートできます。 これは、ソース データベースのセキュリティを重視し、フル クロールと増分クロールの両方を提供するため、ユーザーは常に最新の情報を利用できます。

[[詳細]](https://www.bainsight.com/connectors/mysql-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="netdocuments"></a>NetDocuments

[BA Insight](https://www.bainsight.com/) を利用

NetDocuments コネクタでは、NetDocs に格納されているコンテンツにインデックスを付けるため、ユーザーはポータル内から直接 NetDocuments コンテンツを検索および取得することができます。 このコネクタでは、NetDocs のドキュメント セキュリティが自動的に Azure Cognitive Search に適用されるため、ユーザー情報は安全に保たれます。 NetDocuments に格納されているメタデータは同等の用語にマップできるため、ユーザーはシームレスな検索エクスペリエンスを利用できます。

[[詳細]](https://www.bainsight.com/connectors/netdocuments-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="neudesic-the-firm-directory"></a>Neudesic The Firm Directory

[BA Insight](https://www.bainsight.com/) を利用

Firm Directory コネクタでは、ソース システムのセキュリティを重視し、フル クロールと増分クロールの両方を提供するため、ユーザーは常に最新の情報を利用できます。

[[詳細]](https://www.bainsight.com/connectors/the-firm-directory-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="notes"></a>メモ

[Raytion](https://www.raytion.com/contact) を利用

IBM Notes (以前の Lotus Note) からのコンテンツに確実にインデックスを付け、Azure Cognitive Search でインテリジェントに検索するための安全なエンタープライズ検索コネクタです。 Notes データベースの構成可能なセットからのレコードに対して、ほぼリアルタイムで確実にインデックスを付けます。 このコネクタでは、IBM Notes の組み込みのユーザーとグループの管理を完全にサポートしています。

[[詳細]](https://www.raytion.com/connectors/raytion-notes-connector)

:::column-end:::
:::column span="":::

---

### <a name="nuxeo"></a>Nuxeo

[BA Insight](https://www.bainsight.com/) を利用

Nuxeo コネクタを使用すると、組織は、セキュリティ情報と、コンテンツに設定された標準およびカスタム メタデータの両方を含む Nuxeo コンテンツを、Office 365 に存在するコンテンツと共に Azure Cognitive Search にインデックス付けできます。 最終的に、ユーザーは必要なものすべてを 1 か所で見つけることができます。

[[詳細]](https://www.bainsight.com/connectors/nuxeo-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="objective"></a>目的

[BA Insight](https://www.bainsight.com/) を利用

Objective コネクタは Objective 用に開発されており、Objective への安全な接続を確立し、メタデータを含むコンテンツを Objective スキーマから検索エンジン スキーマにマップします。 その後、コンテンツを抽出し、クロールと呼ばれるプロセスで検索エンジンにフィードします。

[[詳細]](https://www.bainsight.com/connectors/objective-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="onedrive"></a>OneDrive

[Accenture](https://www.accenture.com) を利用

OneDrive コネクタでは、Microsoft OneDrive からのコンテンツをクロールするため、増分クロール、正規表現パターンに基づいた項目の追加や除外、メタデータの抽出、複数の種類のドキュメントの取得が可能になります。

[[詳細]](https://contentanalytics.digital.accenture.com/display/aspire40/OneDrive+Connector)

:::column-end:::
:::column span="":::

---

### <a name="onedrive-for-business"></a>OneDrive for Business

[BA Insight](https://www.bainsight.com/) を利用

BA Insight OneDrive コネクタでは、OneDrive からのコンテンツをさまざまな検索プラットフォームにインデックス付けするため、複数のソースからの統合された検索結果をユーザーに提供できます。

[[詳細]](https://www.bainsight.com/connectors/onedrive-business-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="opentext-content-server"></a>OpenText Content Server

[BA Insight](https://www.bainsight.com/) を利用

このコネクタでは、ネイティブ ポータル コンテンツとほぼ同じようにコンテンツ サーバーのコンテンツにインデックスを付け、フル クロールと増分クロールの両方をサポートします。 Content Server 内で定義されたセキュリティは検索エクスペリエンスに自動的に反映されるため、ユーザーは認可されたコンテンツだけを確実に参照できます。

[[詳細]](https://www.bainsight.com/connectors/livelink-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="opentext-content-server"></a>OpenText Content Server

[Raytion](https://www.raytion.com/contact) を利用

OpenText Content Server からのコンテンツに確実にインデックスを付け、Azure Cognitive Search でインテリジェントに検索するための安全なエンタープライズ検索コネクタです。 コンテンツ サーバー インスタンスからのファイル、フォルダー、仮想フォルダー、複合ドキュメント、ニュース、電子メール、ボリューム、コレクション、分類など、多数のオブジェクトに対して、ほぼリアルタイムで確実にインデックスを付けます。 このコネクタでは、OpenText Content Server の組み込みのユーザーとグループの管理を完全にサポートしています。

[[詳細]](https://www.raytion.com/connectors/raytion-opentext-content-server-connector)

:::column-end:::
:::column span="":::

---

### <a name="opentext-documentum-cloud"></a>OpenText Documentum (クラウド)

[BA Insight](https://www.bainsight.com/) を利用

BA Insight の OpenText Documentum Cloud コネクタでは、Documentum オブジェクトのフル テキストとメタデータの両方を検索エンジンに安全にインデックス付けすることで、複数のリポジトリのコンテンツ全体で 1 つの検索可能な結果セットを有効にします。

[[詳細]](https://www.bainsight.com/connectors/connector-for-documentum-cloud/)

:::column-end:::
:::column span="":::

---

### <a name="opentext-documentum-eroom"></a>OpenText Documentum eRoom

[Raytion](https://www.raytion.com/contact) を利用

OpenText Documentum eRoom からのコンテンツに確実にインデックスを付け、Azure Cognitive Search でインテリジェントに検索するための安全なエンタープライズ検索コネクタです。 Documentum eRoom からのメタデータとプロパティと共に、リポジトリ、フォルダー、ファイルに対して、ほぼリアルタイムで確実にインデックスを付けます。 このコネクタでは、OpenText Documentum eRoom の組み込みのユーザーとグループの管理を完全にサポートしています。

[[詳細]](https://www.raytion.com/connectors/raytion-opentext-documentum-eroom-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="opentext-edocs-dm"></a>OpenText eDOCS DM

[BA Insight](https://www.bainsight.com/) を利用

OpenText eDOCS DM コネクタのユーザーは、eDOCS リポジトリに格納されているコンテンツを Azure Cognitive Search 内から直接検索できるため、必要なコンテンツを見つけるために複数の検索を実行する必要がなくなります。 eDOCS 内で確立されたセキュリティは、アクセスが許可されているユーザーだけがコンテンツを表示できるようにコネクタによって維持されます。

[[詳細]](https://www.bainsight.com/connectors/edocs-dm-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="oracle-database"></a>Oracle Database

[BA Insight](https://www.bainsight.com/) を利用

Oracle Database コネクタは、業界標準のデータベース アクセス方法に基づいて構築されているので、Microsoft SQL Server、MySQL、IBM DB2 などの他のシステムのデータベースも同様にサポートできます。

[[詳細]](https://www.bainsight.com/connectors/oracle-database-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="oracle-webcenter"></a>Oracle WebCenter

[BA Insight](https://www.bainsight.com/) を利用

WebCenter コネクタでは WebCenter と Azure Cognitive Search を統合しているため、組織全体のユーザーが、直接ログインして個別の検索を実行しなくても、WebCenter に格納されている重要な情報を簡単に見つけることができます。

[[詳細]](https://www.bainsight.com/connectors/oracle-webcenter-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="oracle-ka-cloud"></a>Oracle KA Cloud

[Raytion](https://www.raytion.com/contact) を利用

Oracle Knowledge Advanced (KA) クラウドからのコンテンツに確実にインデックスを付け、Azure Cognitive Search でインテリジェントに検索するための安全なエンタープライズ検索コネクタです。 Oracle KA Cloud からのページと添付ファイルに対して、ほぼリアルタイムで確実にインデックスを付けます。 このコネクタでは、Oracle KA Cloud の組み込みのユーザーとグループの管理を完全にサポートしています。 特に、このコネクタでは Oracle KA Cloud ページ内のスニペットベースのアクセス許可を処理します。

[[詳細]](https://www.raytion.com/connectors/raytion-oracle-ka-cloud-connector)

:::column-end:::
:::column span="":::

---

### <a name="oracle-webcenter-content-ucmstellent"></a>Oracle WebCenter Content (UCM/Stellent)

[BA Insight](https://www.bainsight.com/) を利用

WebCenter Content コネクタでは、Azure Cognitive Search で使用できるすべてのコンテンツの基盤となるセキュリティを完全にサポートし、スケジュールされたクロールによってこのコンテンツを最新の状態に保ち、ユーザーが検索を行うときに最新の更新を確実に取得できるようにします。

[[詳細]](https://www.bainsight.com/connectors/oracle-webcenter-content-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="pirobase-cms"></a>pirobase CMS

[Raytion](https://www.raytion.com/contact) を利用

pirobase CMS からのコンテンツに確実にインデックスを付け、Azure Cognitive Search でインテリジェントに検索するための安全なエンタープライズ検索コネクタです。 pirobase CMS からのページ、添付ファイル、その他の生成されたドキュメントの種類に対して、ほぼリアルタイムで確実にインデックスを付けます。 このコネクタでは、pirobase CMS の組み込みのユーザーとグループの管理を完全にサポートしています。

[[詳細]](https://www.raytion.com/connectors/raytion-pirobase-cms-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="postgresql"></a>PostgreSQL

[BA Insight](https://www.bainsight.com/) を利用

BA Insight の PostgreSQL コネクタでは、ソース データベースのセキュリティを重視し、フル クロールと増分クロールを提供するため、ユーザーは常に最新の情報を利用できます。 PostgreSQL からのコンテンツを Azure Cognitive Search にインデックス付けし、BA Insight の SmartHub を通じて表示して、統合された検索結果をユーザーに提供します。

[[詳細]](https://www.bainsight.com/connectors/postgresql-connector-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="practical-law"></a>Practical Law

[BA Insight](https://www.bainsight.com/) を利用

BA Insight Practical Law コネクタを使用すると、ユーザーは Practical Law データベースに対して検索を実行できるため、Practical Law に直接アクセスする必要がなくなります。

[[詳細]](https://www.bainsight.com/connectors/practical-law-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="prolaw"></a>ProLaw

[BA Insight](https://www.bainsight.com/) を利用

ProLaw 用 BA Insight コネクタでは、任意のポータルを ProLaw に接続し、ProLaw 内のユーザー特権を尊重しながら ProLaw からの情報を表示できるようにします。

[[詳細]](https://www.bainsight.com/connectors/prolaw-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="rdb-via-snapshots"></a>RDB via Snapshots

[Accenture](https://www.accenture.com) を利用

RDB via Snapshots コネクタでは、JDBC を使用してアクセスできる任意のリレーショナル データベースからのコンテンツをクロールし、スナップショット データベースを使用して増分クロールを実行します。 SQL ステートメントに基づいて直接データを抽出します。

[[詳細]](https://contentanalytics.digital.accenture.com/display/aspire40/RDB+via+Snapshots+Connector)

:::column-end:::
:::column span="":::

---

### <a name="rdb-via-tables"></a>RDB via Tables

[Accenture](https://www.accenture.com) を利用

RDB via Tables コネクタでは、JDBC を使用してアクセスできる任意のリレーショナル データベースからのコンテンツをクロールし、更新されたコンテンツの識別子を保持するテーブルを使用して更新を取得する増分クロールを実行します。 SQL ステートメントに基づいて直接データを抽出します。

[[詳細]](https://contentanalytics.digital.accenture.com/display/aspire40/RDB+via+Table+Connector)

:::column-end:::
:::column span="":::

---

### <a name="s3"></a>S3

[Accenture](https://www.accenture.com) を利用

Amazon S3 コネクタでは、Amazon Simple Storage Service からのコンテンツをクロールします。 増分クロールを実行し、S3 ドキュメント レベルのセキュリティのためにオブジェクト ACL を取得し、バケット、フォルダー、サブフォルダーに格納されているドキュメントを含みます。

[[詳細]](https://contentanalytics.digital.accenture.com/display/aspire40/Amazon+S3+Connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="salesforce"></a>Salesforce

[Accenture](https://www.accenture.com) を利用

Salesforce コネクタでは、Salesforce リポジトリからのコンテンツをクロールします。 このコネクタでは、Salesforce のナレッジ ベースと Chatter、メタデータとカスタム メタデータの取得をサポートし、フルまたは増分クロールを実行し、ACL、選択できる要素の種類、グループ拡張をフェッチします。

[[詳細]](https://contentanalytics.digital.accenture.com/display/aspire40/Salesforce+Connector)

:::column-end:::
:::column span="":::

---

### <a name="salesforce"></a>Salesforce

[BA Insight](https://www.bainsight.com/) を利用

Salesforce コネクタでは、Salesforce のサービス、営業、マーケティング クラウドを Azure Cognitive Search と統合しているため、すべての従業員が Salesforce 内のすべてのコンテンツをこのポータルを通じて利用できます。

[[詳細]](https://www.bainsight.com/connectors/salesforce-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="salesforce"></a>Salesforce

[Raytion](https://www.raytion.com/contact) を利用

Salesforce からのコンテンツに確実にインデックスを付け、Azure Cognitive Search でインテリジェントに検索するための安全なエンタープライズ検索コネクタです。 Salesforce からのアカウント、チャット メッセージ、プロファイル、リード、ケース、その他すべてのレコード オブジェクトに対して、ほぼリアルタイムで確実にインデックスを付けます。 このコネクタでは、Salesforce の組み込みのユーザーとグループの管理を完全にサポートしています。

[[詳細]](https://www.raytion.com/connectors/raytion-salesforce-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="sap-erp"></a>SAP ERP

[BA Insight](https://www.bainsight.com/) を利用

BA Insight の SAP ERP コネクタは、SAP から検索インデックスに項目を取り込むように設計されています。 これにより、UI が点灯し、SAP、SharePoint、その他のシステムの情報全体で統一されたビューが可能になります。

[[詳細]](https://www.bainsight.com/connectors/sap-erp-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="sap-erp-cloud"></a>SAP ERP (クラウド)

[BA Insight](https://www.bainsight.com/) を利用

BA Insight の SAP ERP (クラウド バージョン) コネクタは、SAP から検索インデックスに項目を取り込むように設計されています。

[[詳細]](https://www.bainsight.com/connectors/connector-for-sap-erp-cloud/)

:::column-end:::
:::column span="":::

---

### <a name="sap-hana"></a>SAP HANA

[BA Insight](https://www.bainsight.com/) を利用

SAP HANA コネクタでは、ソース データベースのセキュリティを重視し、フル クロールと増分クロールの両方を提供するため、ユーザーは常に最新の情報を利用できます。 SAP HANA からのコンテンツを Azure Cognitive Search にインデックス付けし、BA Insight の SmartHub を通じて表示して、統合された検索結果をユーザーに提供します。

[[詳細]](https://www.bainsight.com/connectors/sap-hana-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="sap-hana-cloud"></a>SAP HANA (クラウド)

[BA Insight](https://www.bainsight.com/) を利用

SAP HANA (クラウド バージョン) コネクタでは、ソース データベースのセキュリティを重視し、フル クロールと増分クロールの両方を提供するため、ユーザーは常に最新の情報を利用できます。

[[詳細]](https://www.bainsight.com/connectors/connector-sap-hana-cloud-version/)

:::column-end:::
:::column span="":::

---

### <a name="sap-netweaver-portal"></a>SAP NetWeaver Portal

[Raytion](https://www.raytion.com/contact) を利用

SAP NetWeaver Portal (NWP) からのコンテンツに確実にインデックスを付け、Azure Cognitive Search でインテリジェントに検索するための安全なエンタープライズ検索コネクタです。 SAP NWP とそのナレッジ マネージメント、コラボレーション (KMC)、ポータル コンテンツ ディレクトリ (PCD) 領域からのページ、添付ファイル、その他のドキュメントの種類に対して、ほぼリアルタイムで確実にインデックスを付けます。 このコネクタでは、SAP NetWeaver Portal の組み込みのユーザーとグループの管理、Active Directory とその他のディレクトリ サービスに基づく SAP NWP のインストールを完全にサポートしています。

[[詳細]](https://www.raytion.com/connectors/raytion-sap-netweaver-portal-connector)

:::column-end:::
:::column span="":::

---

### <a name="sap-plm-dms"></a>SAP PLM DMS

[Raytion](https://www.raytion.com/contact) を利用

SAP PLM DMS からのコンテンツに確実にインデックスを付け、Azure Cognitive Search でインテリジェントに検索するための安全なエンタープライズ検索コネクタです。 SAP PLM DMS からのドキュメント、添付ファイル、その他のレコードに対して、ほぼリアルタイムで確実にインデックスを付けます。

[[詳細]](https://www.raytion.com/connectors/raytion-sap-plm-dms-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="selenium"></a>Selenium

[Accenture](https://www.accenture.com) を利用

Selenium コネクタでは、インターネット ブラウザーを使用して Web サイトからのコンテンツをクロールし、Web ページ、サイトマップ、バイナリ ドキュメントなど、複数の種類のドキュメントを取得します。 これにより、Angular や React のようなフレームワークとの互換性の問題が回避されます。

[[詳細]](https://contentanalytics.digital.accenture.com/display/aspire40/Selenium+Crawler)

:::column-end:::
:::column span="":::

---

### <a name="servicenow"></a>ServiceNow

[Accenture](https://www.accenture.com) を利用

ServiceNow コネクタでは、ナレッジ記事、カタログ項目、添付ファイルなど、複数の種類のドキュメントを ServiceNow リポジトリから取得します。 また、セキュリティ ACL を取得し、グループ拡張を実行します。

[[詳細]](https://contentanalytics.digital.accenture.com/display/aspire40/ServiceNow+Connector)

:::column-end:::
:::column span="":::

---

### <a name="servicenow"></a>ServiceNow

[BA Insight](https://www.bainsight.com/) を利用

 ServiceNow コネクタでは、ソース システムのセキュリティを重視し、フル クロールと増分クロールの両方を提供するため、ユーザーは常に最新の情報を利用できます。

[[詳細]](https://www.bainsight.com/connectors/servicenow-connector-sharepoint-azure-elasticsearch)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="servicenow"></a>ServiceNow

[Raytion](https://www.raytion.com/contact) を利用

ServiceNow からのコンテンツに確実にインデックスを付け、Azure Cognitive Search でインテリジェントに検索するための安全なエンタープライズ検索コネクタです。 ServiceNow からの問題、タスク、添付ファイル、ナレッジ マネージメントの記事、ページなどに対して、ほぼリアルタイムで確実にインデックスを付けます。 このコネクタでは、ServiceNow の組み込みのユーザーとグループの管理をサポートしています。

[[詳細]](https://www.raytion.com/connectors/raytion-servicenow-connector)

:::column-end:::
:::column span="":::

---

### <a name="sharepoint"></a>SharePoint

[Raytion](https://www.raytion.com/contact) を利用

Microsoft SharePoint からのコンテンツに確実にインデックスを付け、Azure Cognitive Search でインテリジェントに検索するための安全なエンタープライズ検索コネクタです。 SharePoint オンプレミス インスタンスからのサイト、Web、最新 (SharePoint 2016 以降) およびクラシックのページ、Wiki ページ、OneNote ドキュメント、リスト項目、タスク、カレンダー項目、添付ファイル、ファイルに対して、ほぼリアルタイムで確実にインデックスを付けます。 このコネクタでは、Microsoft SharePoint の組み込みのユーザーとグループの管理、Active Directory および SiteMinder や Okta などの OAuth プロバイダーを完全にサポートしています。 このコネクタには、Basic、NTLM、Kerberos 認証のサポートが含まれています。

[[詳細]](https://www.raytion.com/connectors/raytion-sharepoint-connector)

:::column-end:::
:::column span="":::

---

### <a name="sharepoint-2010"></a>SharePoint 2010

[BA Insight](https://www.bainsight.com/) を利用

BA Insight の SharePoint 2010 コネクタを使用すると、SharePoint 2010 に接続し、任意のサイト、ドキュメント ライブラリ、またはリストからデータを取得し、コンテンツに安全にインデックス付けできます。

[[詳細]](https://www.bainsight.com/connectors/sharepoint-2010-connector/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="sharepoint-2013"></a>SharePoint 2013

[Accenture](https://www.accenture.com) を利用

SharePoint 2013 コネクタでは、SharePoint 2013 のサイト コレクションの URL からのコンテンツをクロールします。 SharePoint の変更ログ タイムスタンプまたはスナップショット データベースを使用して増分クロールを実行し、ACL を取得し、NTLM と HTTPS、BCS 外部リストをサポートし、SharePoint に何もインストールせずに実行します。

[[詳細]](https://contentanalytics.digital.accenture.com/display/aspire40/SharePoint+2013+Connector)

:::column-end:::
:::column span="":::

---

### <a name="sharepoint-2013"></a>SharePoint 2013

[BA Insight](https://www.bainsight.com/) を利用

BA Insight の SharePoint 2013 コネクタを使用すると、SharePoint 2013 に接続し、任意のサイト、ドキュメント ライブラリ、またはリストからデータを取得し、コンテンツに安全にインデックス付けできます。

[[詳細]](https://www.bainsight.com/connectors/sharepoint-2013-connector/)

:::column-end:::
:::column span="":::

---

### <a name="sharepoint-2016"></a>SharePoint 2016

[Accenture](https://www.accenture.com) を利用

SharePoint 2016 コネクタでは、SharePoint 2016 のサイト コレクションの URL からのコンテンツをクロールします。 SharePoint の変更ログ タイムスタンプまたはスナップショット データベースを使用して増分クロールを実行し、ACL を取得し、NTLM と HTTPS、BCS 外部リストをサポートし、SharePoint に何もインストールせずに実行します。

[[詳細]](https://contentanalytics.digital.accenture.com/display/aspire40/SharePoint+2016+Connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="sharepoint-2016"></a>SharePoint 2016

[BA Insight](https://www.bainsight.com/) を利用

BA Insight の SharePoint コネクタを使用すると、SharePoint 2016 に接続し、任意のサイト、ドキュメント ライブラリ、またはリストからデータを取得し、コンテンツに安全にインデックス付けできます。

[[詳細]](https://www.bainsight.com/connectors/sharepoint-2016-connector/)

:::column-end:::
:::column span="":::

---

### <a name="sharepoint-2019"></a>SharePoint 2019

[BA Insight](https://www.bainsight.com/) を利用

BA Insight の SharePoint コネクタを使用すると、SharePoint 2019 に接続し、任意のサイト、ドキュメント ライブラリ、またはリストからデータを取得し、コンテンツに安全にインデックス付けできます。

[[詳細]](https://www.bainsight.com/connectors/connector-for-sharepoint-2019/)

:::column-end:::
:::column span="":::

---

### <a name="sharepoint-online"></a>SharePoint Online

[Accenture](https://www.accenture.com) を利用

SharePoint Online コネクタでは、SharePoint Online のサイト コレクションの URL からのコンテンツをクロールします。 このコネクタでは、サイト、リスト、フォルダー、リスト アイテム、添付ファイル、および他のページ (.aspx 形式) が取得されます。 Microsoft O365 オファリングで実行されている SharePoint をサポートします。

[[詳細]](https://contentanalytics.digital.accenture.com/display/aspire40/SharePoint+Online+Connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="sharepoint-online"></a>SharePoint Online

[BA Insight](https://www.bainsight.com/) を利用

BA Insight の SharePoint Online コネクタを使用すると、SharePoint Online に接続し、任意のサイト、ドキュメント ライブラリ、またはリストからデータを取得し、コンテンツに安全にインデックス付けできます。

[[詳細]](https://www.bainsight.com/connectors/sharepoint-online-connector/)

:::column-end:::
:::column span="":::

---

### <a name="sitecore"></a>Sitecore

[BA Insight](https://www.bainsight.com/) を利用

Sitecore コネクタでは、ソース システムのセキュリティを重視し、フル クロールと増分クロールの両方を提供するため、ユーザーは常に最新の情報を利用できます。

[[詳細]](https://www.bainsight.com/connectors/sitecore-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="sitecore"></a>Sitecore

[Raytion](https://www.raytion.com/contact) を利用

Sitecore からのコンテンツに確実にインデックスを付け、Azure Cognitive Search でインテリジェントに検索するための安全なエンタープライズ検索コネクタです。 ページ、添付ファイル、その他の生成されたドキュメントの種類に対して、ほぼリアルタイムで確実にインデックスを付けます。 このコネクタでは、Sitecore のアクセス許可モデルと、関連付けられている Active Directory でのユーザーとグループの管理を完全にサポートします。

[[詳細]](https://www.raytion.com/connectors/raytion-sitecore-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="slack"></a>Slack

[Raytion](https://www.raytion.com/contact) を利用

Slack からのコンテンツに確実にインデックスを付け、Azure Cognitive Search でインテリジェントに検索するための安全なエンタープライズ検索コネクタです。 Slack のすべてのパブリック チャネルからのメッセージ、スレッド、共有ファイルに対して、ほぼリアルタイムで確実にインデックスを付けます。

[[詳細]](https://www.raytion.com/connectors/raytion-slack-connector)

:::column-end:::
:::column span="":::

---

### <a name="smb"></a>SMB

[Accenture](https://www.accenture.com) を利用

SMB コネクタでは、共有ドライブ間でファイルとディレクトリを取得します。 分散ファイル システムのサポート、セキュリティ情報の取得が可能であり、最後にアクセスした日付を変更せずに、インデックス作成のためにドキュメントにアクセスできます。

[[詳細]](https://contentanalytics.digital.accenture.com/display/aspire40/SMB+Connector)

:::column-end:::
:::column span="":::

---

### <a name="smb-file-share"></a>SMB ファイル共有

[Raytion](https://www.raytion.com/contact) を利用

SMB ファイル共有からのコンテンツに確実にインデックスを付け、Azure Cognitive Search でインテリジェントに検索するための安全なエンタープライズ検索コネクタです。 ファイル共有からのファイルとフォルダーに対して、ほぼリアルタイムで確実にインデックスを付けます。 このコネクタでは、SMB のドキュメント レベルのセキュリティと、SMB2 および SMB3 プロトコルの最新バージョンを完全にサポートしています。

[[詳細]](https://www.raytion.com/connectors/raytion-smb-file-share-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="sql-database"></a>SQL Database

[Raytion](https://www.raytion.com/contact) を利用

Microsoft SQL Server や Oracle などの SQL データベースからのコンテンツに確実にインデックスを付け、Azure Cognitive Search でインテリジェントに検索するための安全なエンタープライズ検索コネクタです。 SQL データベースからのバイナリ ドキュメントを含むレコードとフィールドに対して、ほぼリアルタイムで確実にインデックスを付けます。 このコネクタでは、カスタムのドキュメント レベルのセキュリティ モデルの実装をサポートしています。

[[詳細]](https://www.raytion.com/connectors/raytion-sql-database-connector)

:::column-end:::
:::column span="":::

---

### <a name="any-sql-based-crm-system"></a>SQL ベースの CRM システム

[BA Insight](https://www.bainsight.com/) を利用

SQL Server コネクタは、業界標準のデータベース アクセス方法に基づいて構築されているので、Oracle、MySQL、IBM DB2 などの他のシステムのデータベースも同様にサポートできます。 これは、ソース データベースのセキュリティを重視し、フル クロールと増分クロールの両方を提供するため、ユーザーは常に最新の情報を利用できます。

[[詳細]](https://www.bainsight.com/connectors/sql-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="symantec-enterprise-vault"></a>Symantec Enterprise Vault

[Raytion](https://www.raytion.com/contact) を利用

Symantec Enterprise Vault からのコンテンツに確実にインデックスを付け、Azure Cognitive Search でインテリジェントに検索するための安全なエンタープライズ検索コネクタです。 Enterprise Vault からの電子メール、添付ファイル、ファイル、カレンダー項目、連絡先などのアーカイブ済みデータに対して、ほぼリアルタイムで確実にインデックスを付けます。 このコネクタでは、Symantec Enterprise Vault の認証モデルである Basic、NTLM、Kerberos 認証を完全にサポートしています。

[[詳細]](https://www.raytion.com/connectors/raytion-enterprise-vault-connector-2)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="twitter"></a>Twitter

[Accenture](https://www.accenture.com) を利用

Twitter コネクタでは、任意の Twitter アカウントからのコンテンツをクロールします。 フル クロールと増分クロールを実行し、Twitter ユーザー、コンシューマー キー、コンシューマー シークレット キーを使用した認証をサポートします。

[[詳細]](https://contentanalytics.digital.accenture.com/display/aspire40/Twitter+Connector)

:::column-end:::
:::column span="":::

---

### <a name="veeva-vault"></a>Veeva Vault

[BA Insight](https://www.bainsight.com/) を利用

BA Insight の Veeva Vault コネクタでは、Veeva Vault オブジェクトのフル テキストとメタデータの両方を Azure Cognitive Search に安全にインデックス付けします。 これにより、ユーザーは Veeva Vault および Microsoft 365 内のコンテンツに対する単一の結果セットを取得できます。

[[詳細]](https://www.bainsight.com/connectors/veeva-vault-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="veritas-enterprise-vault-symantec-evault"></a>Veritas Enterprise Vault (Symantec eVault)

[BA Insight](https://www.bainsight.com/) を利用

Veritas Enterprise Vault コネクタでは、ソース システムのセキュリティを重視し、フル クロールと増分クロールを提供するため、ユーザーは常に最新の情報を利用できます。

[[詳細]](https://www.bainsight.com/connectors/enterprise-vault-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="veritas-enterprise-vault"></a>Veritas Enterprise Vault

[Raytion](https://www.raytion.com/contact) を利用

Veritas Enterprise Vault からのコンテンツに確実にインデックスを付け、Azure Cognitive Search でインテリジェントに検索するための安全なエンタープライズ検索コネクタです。 Enterprise Vault からの電子メール、添付ファイル、ファイル、カレンダー項目、連絡先などのアーカイブ済みデータに対して、ほぼリアルタイムで確実にインデックスを付けます。 このコネクタでは、Veritas Enterprise Vault の認証モデルである Basic、NTLM、Kerberos 認証を完全にサポートしています。

[[詳細]](https://www.raytion.com/connectors/raytion-enterprise-vault-connector)

:::column-end:::
:::column span="":::
---

### <a name="website-crawler"></a>Website Crawler

[BA Insight](https://www.bainsight.com/) を利用

BA Insight Website Crawler コネクタを使用すると、1 つの統合された検索インデックス内の Web サイトのコンテンツと、他のリポジトリのコンテンツを表示することができます。

[[詳細]](https://www.bainsight.com/connectors/website-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="west-km"></a>West km

[BA Insight](https://www.bainsight.com/) を利用

BA Insight West km コネクタでは、カスタムの検索結果ページの作成を含め、業務と訴訟のドキュメント全体の検索をサポートしています。

[[詳細]](https://www.bainsight.com/connectors/westkm-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="windream-ecm-system"></a>windream ECM-System

[Raytion](https://www.raytion.com/contact) を利用

windream ECM-System からのコンテンツに確実にインデックスを付け、Azure Cognitive Search でインテリジェントに検索するための安全なエンタープライズ検索コネクタです。 windream ECM-System によって関連付けられたメタデータの包括的なセットを含むファイルとフォルダーに対して、ほぼリアルタイムで確実にインデックスを付けます。 このコネクタでは、windream ECM-System のアクセス許可モデルと、関連付けられている Active Directory でのユーザーとグループの管理を完全にサポートします。

[[詳細]](https://www.raytion.com/connectors/raytion-windream-ecm-system-connector)

:::column-end:::
:::column span="":::

---

### <a name="xerox-docushare"></a>Xerox DocuShare

[BA Insight](https://www.bainsight.com/) を利用

Docushare リポジトリに格納されているコンテンツを Azure Cognitive Search 内から直接検索するため、必要なコンテンツを見つけるために複数の検索を実行する必要がなくなります。

[[詳細]](https://www.bainsight.com/connectors/docushare-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::
---

### <a name="xerox-docushare"></a>Xerox DocuShare

[Raytion](https://www.raytion.com/contact) を利用

Xerox DocuShare からのコンテンツに確実にインデックスを付け、Azure Cognitive Search でインテリジェントに検索するための安全なエンタープライズ検索コネクタです。 DocuShare からのデータ リポジトリ、フォルダー、プロファイル、グループ、ファイルに対して、ほぼリアルタイムで確実にインデックスを付けます。 このコネクタでは、Xerox DocuShare の組み込みのユーザーとグループの管理を完全にサポートしています。

[[詳細]](https://www.raytion.com/connectors/raytion-xerox-docushare-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="yammer"></a>Yammer

[Accenture](https://www.accenture.com) を利用

Yammer コネクタでは、Yammer メッセージからのコンテンツをクロールします。 グループ、スレッド、またはトピック別にメッセージを取得し、ユーザー、グループ、スレッドの詳細を取得します。

[[詳細]](https://contentanalytics.digital.accenture.com/display/aspire40/Yammer+Connector)

:::column-end:::
:::column span="":::

---

### <a name="yammer"></a>Yammer

[BA Insight](https://www.bainsight.com/) を利用

Yammer コネクタでは、Yammer アプリケーションへの安全な接続を確立し、メタデータや添付ファイルなどのコンテンツを Yammer スキーマから検索エンジン スキーマにマップします。 その後、コンテンツを抽出し、クロールと呼ばれるプロセスで検索エンジンにフィードします。

[[詳細]](https://www.bainsight.com/connectors/yammer-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="yammer"></a>Yammer

[Raytion](https://www.raytion.com/contact) を利用

Microsoft Yammer からのコンテンツに確実にインデックスを付け、Azure Cognitive Search でインテリジェントに検索するための安全なエンタープライズ検索コネクタです。 Yammer からのチャネル、投稿、返信、添付ファイル、投票、お知らせに対して、ほぼリアルタイムで確実にインデックスを付けます。 このコネクタでは、Microsoft Yammer の組み込みのユーザーとグループの管理、特に Microsoft 365 に対するフェデレーション認証を完全にサポートしています。

[[詳細]](https://www.raytion.com/connectors/raytion-yammer-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---
':::column-end::': null
':::column span=""::': null
---

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::
