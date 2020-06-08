---
title: マネージド ID を使用してストレージ アカウントへの接続を設定する (プレビュー)
titleSuffix: Azure Cognitive Search
description: マネージド ID を使用して、Azure ストレージ アカウントへのインデクサー接続を設定する方法を学ぶ (プレビュー)
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 1fa9581f1a7968a1bfd6df0fb82383dd45e70f54
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83663180"
---
# <a name="set-up-a-connection-to-an-azure-storage-account-using-a-managed-identity-preview"></a>マネージド ID を使用して、Azure ストレージ アカウントへの接続を設定する (プレビュー)

> [!IMPORTANT] 
> マネージド ID を使用する、データ ソースへの接続の設定のサポートは現在、限定的なパブリック プレビューの段階です。 プレビュー段階の機能はサービス レベル アグリーメントなしで提供しています。運用環境のワークロードに使用することはお勧めできません。
> プレビューへのアクセスの要求は、[こちらのフォーム](https://aka.ms/azure-cognitive-search/mi-preview-request)に入力して行うことができます。

このページでは、データ ソースのオブジェクト接続文字列で資格情報を指定する代わりに、マネージド ID を使用して Azure ストレージ アカウントへのインデクサー接続を設定する方法を説明します。

この機能についてさらに学ぶ前に、インデクサーとは何かについて、およびデータ ソースに対してインデクサーを設定する方法について理解しておくことをお勧めします。 以下のリンクで詳しい情報を確認できます。
* [インデクサーの概要](search-indexer-overview.md)
* [Azure BLOB インデクサー](search-howto-indexing-azure-blob-storage.md)
* [Azure Data Lake Storage Gen2 インデクサー](search-howto-index-azure-data-lake-storage.md)
* [Azure テーブル インデクサー](search-howto-indexing-azure-tables.md)

## <a name="set-up-the-connection"></a>接続を設定する

### <a name="1---turn-on-system-assigned-managed-identity"></a>1 - システム割り当てマネージド ID をオンにする

システム割り当てマネージド ID が有効になると、Azure で検索サービスのための ID が作成されます。これは、同じテナントとサブスクリプション内の他の Azure サービスに対する認証に使用することができます。 その後、インデックス作成中にデータへのアクセスを許可する、ロールベースのアクセス制御 (RBAC) の割り当てで、この ID を使用することができます。

![システム割り当てマネージド ID をオンにする](./media/search-managed-identities/turn-on-system-assigned-identity.png "システム割り当てマネージド ID をオンにする")

**[保存]** を選択した後に、検索サービスに割り当てられたオブジェクト ID が表示されます。

![オブジェクト ID](./media/search-managed-identities/system-assigned-identity-object-id.png "Object ID")
 
### <a name="2---add-a-role-assignment"></a>2 - ロールの割り当てを追加する

この手順では、Azure Cognitive Search サービスに、ストレージ アカウントからデータを読み取るためのアクセス許可を付与します。

1. Azure portal で、インデックスを作成するデータを含むストレージ アカウントに移動します。
2. **[アクセス制御 (IAM)]** を選択します
3. **[追加]** 、 **[ロールの割り当ての追加]** の順に選択します

    ![ロールの割り当てを追加する](./media/search-managed-identities/add-role-assignment-storage.png "ロールの割り当ての追加")

4. インデックスを作成するストレージ アカウントの種類に基づいて、適切なロールを選択します。
    1. Azure BLOB ストレージでは、検索サービスを、**閲覧者とデータ アクセス**および**ストレージ BLOB データ閲覧者**のロールに追加する必要があります。
    1. Azure Data Lake Storage Gen2 では、検索サービスを、**閲覧者とデータ アクセス**および**ストレージ BLOB データ閲覧者**のロールに追加する必要があります。
    1. Azure テーブル ストレージでは、検索サービスを**閲覧者とデータ アクセス** ロールにのみ追加する必要があります。
5.  **[アクセスの割り当て先]** は **[Azure AD のユーザー、グループ、サービス プリンシパル]** のままにしておきます
6.  検索サービスを検索し、それを選んでから、 **[保存]** を選択します

    ![閲覧者とデータ アクセスのロールの割り当てを追加する](./media/search-managed-identities/add-role-assignment-reader-and-data-access.png "閲覧者とデータ アクセスのロールの割り当てを追加する")

Azure BLOB ストレージと Azure Data Lake Storage Gen2 に接続する場合は、**ストレージ BLOB データ閲覧者**ロールの割り当ても追加する必要があることにご注意ください。

![ストレージ BLOB データ閲覧者ロールの割り当てを追加する](./media/search-managed-identities/add-role-assignment-storage-blob-data-reader.png "ストレージ BLOB データ閲覧者ロールの割り当てを追加する")

### <a name="3---create-the-data-source"></a>3 - データ ソースを作成する

ストレージ アカウントからインデックスを作成する場合は、データ ソースに次の必須プロパティが必要です。

* **name** は、Search サービス内のデータ ソースの一意の名前です。
* **type**
    * Azure BLOB ストレージ: `azureblob`
    * Azure テーブル ストレージ: `azuretable`
    * Azure Data Lake Storage Gen2: [こちらのフォーム](https://aka.ms/azure-cognitive-search/mi-preview-request)を使用してプレビューにサインアップすると、この**種類**が提供されます。
* **credentials**
    * マネージド ID を使用して認証する場合、**credentials** 形式は、マネージド ID を使用しない場合とは異なります。 ここでは、アカウント キーまたはパスワードのない ResourceId を指定します。 ResourceId には、ストレージ アカウントのサブスクリプション ID、ストレージ アカウントのリソース グループ、およびストレージ アカウント名を含める必要があります。
    * マネージド ID の形式: 
        * *ResourceId=/subscriptions/**ご利用のサブスクリプション ID**/resourceGroups/**ご利用のリソース グループの名前**/providers/Microsoft.Storage/storageAccounts/**ご利用のストレージ アカウントの名前**/;*
* **container** では、ストレージ アカウントにあるコンテナーまたはテーブルを指定します。 既定では、コンテナー内のすべての BLOB を取得できます。 特定の仮想ディレクトリにある BLOB についてのみインデックスを作成する場合は、オプションの **query** パラメーターを使用してそのディレクトリを指定できます。

[REST API](https://docs.microsoft.com/rest/api/searchservice/create-data-source) を使用して BLOB データ ソース オブジェクトを作成する方法の例:

```
POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
    "name" : "blob-datasource",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name/;" },
    "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
}   
```

Azure portal と [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet) でも、マネージド ID 接続文字列がサポートされます。 Azure portal では、このページの上部にあるリンクを使用してプレビューにサインアップする際に提供される、機能フラグが必要です。 

### <a name="4---create-the-index"></a>4 - インデックスを作成する

インデックスでは、検索に使用する、ドキュメント内のフィールド、属性、およびその他の構成要素を指定します。

ここでは、BLOB から抽出されたテキストを格納するために、検索可能な `content` フィールドを含むインデックスを作成する方法を示します。   

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }

インデックスの作成の詳細については、[インデックスの作成](https://docs.microsoft.com/rest/api/searchservice/create-index)に関する記事をご覧ください。

### <a name="5---create-the-indexer"></a>5 - インデクサーを作成する

インデクサーはデータ ソースをターゲットの検索インデックスに接続し、データ更新を自動化するスケジュールを提供します。

インデックスとデータ ソースが作成されたら、インデクサーを作成できます。

BLOB インデクサーのインデクサー定義の例:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

このインデクサーは 2 時間ごとに実行されます (スケジュールの間隔が "PT2H" に設定されています)。 インデクサーを 30 分ごとに実行するには、間隔を "PT30M" に設定します。 サポートされている最短の間隔は 5 分です。 スケジュールは省略可能です。省略した場合、インデクサーは作成時に一度だけ実行されます。 ただし、いつでもオンデマンドでインデクサーを実行できます。   

インデクサー作成 API の詳細については、「 [インデクサーの作成](https://docs.microsoft.com/rest/api/searchservice/create-indexer)」をご覧ください。

インデクサーのスケジュールの定義の詳細については、[Azure Cognitive Search のインデクサーのスケジュールを設定する方法](search-howto-schedule-indexers.md)に関する記事を参照してください。

## <a name="see-also"></a>関連項目

Azure Storage インデクサーの詳細については、以下を参照してください。
* [Azure BLOB インデクサー](search-howto-indexing-azure-blob-storage.md)
* [Azure Data Lake Storage Gen2 インデクサー](search-howto-index-azure-data-lake-storage.md)
* [Azure テーブル インデクサー](search-howto-indexing-azure-tables.md)
