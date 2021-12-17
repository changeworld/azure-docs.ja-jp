---
title: マネージド ID を使用してストレージ アカウントへの接続を設定する
titleSuffix: Azure Cognitive Search
description: マネージド ID を使用して、Azure Storage アカウントへのインデクサー接続を設定する方法を学ぶ
author: gmndrg
ms.author: gimondra
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: d714c4cd1b507a8722bb6407fb991a2b158b72e6
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894376"
---
# <a name="set-up-a-connection-to-an-azure-storage-account-using-a-managed-identity"></a>マネージド ID を使用して、Azure Storage アカウントへの接続を設定する

このページでは、データ ソースのオブジェクト接続文字列で資格情報を指定する代わりに、マネージド ID を使用して Azure ストレージ アカウントへのインデクサー接続を設定する方法を説明します。

システム割り当てマネージド ID またはユーザー割り当てマネージド ID (プレビュー) を使用できます。

この記事では、インデクサーの概念と構成に関する理解を前提とします。 インデクサーを知らない場合は、次のリンクから始める必要があります。

* [インデクサーの概要](search-indexer-overview.md)
* [Azure BLOB インデクサー](search-howto-indexing-azure-blob-storage.md)
* [Azure Data Lake Storage Gen2 インデクサー](search-howto-index-azure-data-lake-storage.md)
* [Azure テーブル インデクサー](search-howto-indexing-azure-tables.md)

## <a name="1---set-up-a-managed-identity"></a>1 - マネージド ID を設定する

次のいずれかのオプションを使用して、Azure Cognitive Search サービスの[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を設定します。 

検索サービスは Basic レベル以上である必要があります。

### <a name="option-1---turn-on-system-assigned-managed-identity"></a>オプション 1 - システム割り当てマネージド ID を有効にする

システム割り当てマネージド ID が有効になると、Azure で検索サービスのための ID が作成されます。これは、同じテナントとサブスクリプション内の他の Azure サービスに対する認証に使用することができます。 この ID はその後、インデックス作成中にデータへのアクセスを許可する Azure ロールベースのアクセス制御 (Azure RBAC) の割り当てで、使用することができます。

![システム割り当てマネージド ID をオンにする](./media/search-managed-identities/turn-on-system-assigned-identity.png "システム割り当てマネージド ID をオンにする")

**[保存]** を選択した後に、検索サービスに割り当てられたオブジェクト ID が表示されます。

![オブジェクト ID](./media/search-managed-identities/system-assigned-identity-object-id.png "Object ID")

### <a name="option-2---assign-a-user-assigned-managed-identity-to-the-search-service-preview"></a>オプション 2 - ユーザー割り当てのマネージド ID を検索サービスに割り当てる (プレビュー)

ユーザー割り当てマネージド ID をまだ作成していない場合は、作成する必要があります。 ユーザー割り当てマネージド ID は Azure のリソースです。

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. **[+ リソースの作成]** を選択します。

1. [サービスとマーケットプレース] の検索バーで、[ユーザー割り当てマネージド ID] を検索し、 **[作成]** を選択します。

1. ID にわかりやすい名前を付けます。

次に、ユーザー割り当てマネージド ID を検索サービスに割り当てます。 これは、[2021-04-01-preview 管理 API](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update) を使用して行うことができます。

ID プロパティは、型と 1 つ以上の完全修飾ユーザー割り当て ID を受け取ります。

* **type** は ID の型です。 両方について、有効な値は "SystemAssigned"、"UserAssigned"、または "SystemAssigned, UserAssigned" です。 値 "None" を指定すると、検索サービスから以前に割り当てられた ID がクリアされます。

* **userAssignedIdentities** には、ユーザー割り当てマネージド ID の詳細が含まれます。 形式は次のようになります:

  ```bash
    /subscriptions/<your-subscription-ID>/resourcegroups/<your-resource-group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<your-managed-identity-name>
  ```

ユーザー割り当てマネージド ID の割り当ての例:

```http
PUT https://management.azure.com/subscriptions/[subscription ID]/resourceGroups/[resource group name]/providers/Microsoft.Search/searchServices/[search service name]?api-version=2021-04-01-preview
Content-Type: application/json

{
  "location": "[region]",
  "sku": {
    "name": "[sku]"
  },
  "properties": {
    "replicaCount": [replica count],
    "partitionCount": [partition count],
    "hostingMode": "default"
  },
  "identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/[subscription ID]/resourcegroups/[resource group name]/providers/Microsoft.ManagedIdentity/userAssignedIdentities/[name of managed identity]": {}
    }
  }
} 
```

## <a name="2---add-a-role-assignment"></a>2 - ロールの割り当てを追加する

このステップでは、Azure Cognitive Search サービスまたはユーザー割り当てマネージド ID に、ストレージ アカウントからデータを読み取るためのアクセス許可を付与します。

1. Azure portal で、インデックスを作成するデータを含むストレージ アカウントに移動します。

2. **[アクセス制御 (IAM)]** を選択します

3. **[追加]** 、 **[ロールの割り当ての追加]** の順に選択します

    ![ロールの割り当てを追加する](./media/search-managed-identities/add-role-assignment-storage.png "ロールの割り当ての追加")

4. インデックスを作成するストレージ アカウントの種類に基づいて、適切なロールを選択します。

    * Azure Blob Storage では、**ストレージ BLOB データ閲覧者** ロールに検索サービスを追加する必要があります。
    * Azure Data Lake Storage Gen2 では、**ストレージ BLOB データ閲覧者** ロールに検索サービスを追加する必要があります。
    * Azure Table Storage では、**閲覧者とデータ アクセス** ロールに検索サービスを追加する必要があります。

5. **[アクセスの割り当て先]** は **[Azure AD のユーザー、グループ、サービス プリンシパル]** のままにしておきます

6. システム割り当てマネージド ID を使用している場合、検索サービスを検索して選択します。 ユーザー割り当てマネージド ID を使用している場合、ユーザー割り当てマネージド ID の名前を検索して選択します。 **[保存]** を選択します。

    システム割り当てマネージド ID を使用する Azure Blob Storage および Azure Data Lake Storage Gen2 の例:

    ![ストレージ BLOB データ閲覧者ロールの割り当てを追加する](./media/search-managed-identities/add-role-assignment-storage-blob-data-reader.png "ストレージ BLOB データ閲覧者ロールの割り当てを追加する")

    システム割り当てマネージド ID を使用した Azure Table Storage の例:

    ![閲覧者とデータ アクセスのロールの割り当てを追加する](./media/search-managed-identities/add-role-assignment-reader-and-data-access.png "閲覧者とデータ アクセスのロールの割り当てを追加する")

C# のコード例については、GitHub の「[Index Data Lake Gen2 using Azure AD](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/data-lake-gen2-acl-indexing/README.md)」 (Azure AD を使用して Lake Gen2 でデータのインデックスを作成する) を参照してください。

## <a name="3---create-the-data-source"></a>3 - データ ソースを作成する

データ ソースを作成し、システム割り当てマネージド ID またはユーザー割り当てマネージド ID (プレビュー) のいずれかを指定します。 以下の手順では管理 REST API を使用しなくなったことに注意してください。

### <a name="option-1---create-the-data-source-with-a-system-assigned-managed-identity"></a>オプション 1 - システム割り当てマネージド ID を使用してデータ ソースを作成する

[REST API](/rest/api/searchservice/create-data-source)、Azure portal、および [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) では、システム割り当てマネージド ID の使用がサポートされています。 次に、[REST API](/rest/api/searchservice/create-data-source) とマネージド ID 接続文字列を使用し、ストレージ アカウントのデータにインデックスを付けてデータ ソースを作成する方法例を示します。 マネージド ID 接続文字列の形式は、REST API、.NET SDK、および Azure portal において同じです。

ストレージ アカウントからインデックスを作成する場合は、データ ソースに次の必須プロパティが必要です。

* **name** は、Search サービス内のデータ ソースの一意の名前です。
* **type**
    * Azure Blob Storage: `azureblob`
    * Azure Table Storage: `azuretable`
    * Azure Data Lake Storage Gen2: `adlsgen2`
* **credentials**
    * マネージド ID を使用して認証する場合、**credentials** 形式は、マネージド ID を使用しない場合とは異なります。 ここでは、アカウント キーまたはパスワードのない ResourceId を指定します。 ResourceId には、ストレージ アカウントのサブスクリプション ID、ストレージ アカウントのリソース グループ、およびストレージ アカウント名を含める必要があります。
    * マネージド ID の形式: 
        * *ResourceId=/subscriptions/**ご利用のサブスクリプション ID**/resourceGroups/**ご利用のリソース グループの名前**/providers/Microsoft.Storage/storageAccounts/**ご利用のストレージ アカウントの名前**/;*
* **container** では、ストレージ アカウントにあるコンテナーまたはテーブルを指定します。 既定では、コンテナー内のすべての BLOB を取得できます。 特定の仮想ディレクトリにある BLOB についてのみインデックスを作成する場合は、オプションの **query** パラメーターを使用してそのディレクトリを指定できます。

[REST API](/rest/api/searchservice/create-data-source) を使用して BLOB データ ソース オブジェクトを作成する方法の例:

```http
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "blob-datasource",
    "type" : "azureblob",
    "credentials" : { 
        "connectionString" : "ResourceId=/subscriptions/[subscription ID]/resourceGroups/[resource group name]/providers/Microsoft.Storage/storageAccounts/[storage account name]/;" 
    },
    "container" : { 
        "name" : "my-container", "query" : "<optional-virtual-directory-name>" 
    }
}   
```

### <a name="option-2---create-the-data-source-with-a-user-assigned-managed-identity"></a>オプション 2 - ユーザー割り当てマネージド ID を使用してデータ ソースを作成する

2021-04-30-preview REST API では、ユーザー割り当てマネージド ID がサポートされています。 次に、[REST API](/rest/api/searchservice/create-data-source)、マネージド ID 接続文字列、ユーザー割り当てマネージド ID を使用し、ストレージ アカウントのデータにインデックスを付けるデータ ソースを作成する方法例を示します。

ストレージ アカウントからインデックスを作成する場合は、データ ソースに次の必須プロパティが必要です。

* **name** は、Search サービス内のデータ ソースの一意の名前です。
* **type**
    * Azure Blob Storage: `azureblob`
    * Azure Table Storage: `azuretable`
    * Azure Data Lake Storage Gen2: `adlsgen2`
* **credentials**
    * マネージド ID を使用して認証する場合、**credentials** 形式は、マネージド ID を使用しない場合とは異なります。 ここでは、アカウント キーまたはパスワードのない ResourceId を指定します。 ResourceId には、ストレージ アカウントのサブスクリプション ID、ストレージ アカウントのリソース グループ、およびストレージ アカウント名を含める必要があります。
    * マネージド ID の形式: 
        * *ResourceId=/subscriptions/**ご利用のサブスクリプション ID**/resourceGroups/**ご利用のリソース グループの名前**/providers/Microsoft.Storage/storageAccounts/**ご利用のストレージ アカウントの名前**/;*
* **container** では、ストレージ アカウントにあるコンテナーまたはテーブルを指定します。 既定では、コンテナー内のすべての BLOB を取得できます。 特定の仮想ディレクトリにある BLOB についてのみインデックスを作成する場合は、オプションの **query** パラメーターを使用してそのディレクトリを指定できます。
* **ID** には、ユーザー割り当てマネージド ID のコレクションが含まれます。 データ ソースを作成するとき、ユーザー割り当てマネージド ID を 1 つだけ指定する必要があります。
    * **userAssignedIdentities** には、ユーザー割り当てマネージド ID の詳細が含まれます。
        * ユーザー割り当てマネージド ID の形式: 
            * /subscriptions/**サブスクリプション ID**/resourcegroups/**リソース グループ名**/providers/Microsoft.ManagedIdentity/userAssignedIdentities/**マネージド ID の名前**

[REST API](/rest/api/searchservice/create-data-source) を使用して BLOB データ ソース オブジェクトを作成する方法の例:

```http
POST https://[service name].search.windows.net/datasources?api-version=2021-04-30-preview
Content-Type: application/json
api-key: [admin key]

{
    "name" : "blob-datasource",
    "type" : "azureblob",
    "credentials" : { 
        "connectionString" : "ResourceId=/subscriptions/[subscription ID]/resourceGroups/[resource group name]/providers/Microsoft.Storage/storageAccounts/[storage account name]/;" 
    },
    "container" : { 
        "name" : "my-container", "query" : "<optional-virtual-directory-name>" 
    },
    "identity" : { 
        "@odata.type": "#Microsoft.Azure.Search.DataUserAssignedIdentity",
        "userAssignedIdentity" : "/subscriptions/[subscription ID]/resourcegroups/[resource group name]/providers/Microsoft.ManagedIdentity/userAssignedIdentities/[managed identity name]" 
    }
}   
```

## <a name="4---create-the-index"></a>4 - インデックスを作成する

インデックスでは、検索に使用する、ドキュメント内のフィールド、属性、およびその他の構成要素を指定します。

ここでは、BLOB から抽出されたテキストを格納するために、検索可能な `content` フィールドを含むインデックスを作成する方法を示します。   

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }
```

インデックスの作成の詳細については、[インデックスの作成](/rest/api/searchservice/create-index)に関する記事をご覧ください。

## <a name="5---create-the-indexer"></a>5 - インデクサーを作成する

インデクサーはデータ ソースをターゲットの検索インデックスに接続し、データ更新を自動化するスケジュールを提供します。

インデックスとデータ ソースが作成されたら、インデクサーを作成できます。

BLOB インデクサーのインデクサー定義の例:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

このインデクサーは 2 時間ごとに実行されます (スケジュールの間隔が "PT2H" に設定されています)。 インデクサーを 30 分ごとに実行するには、間隔を "PT30M" に設定します。 サポートされている最短の間隔は 5 分です。 スケジュールは省略可能です。省略した場合、インデクサーは作成時に一度だけ実行されます。 ただし、いつでもオンデマンドでインデクサーを実行できます。   

インデクサー作成 API の詳細については、「 [インデクサーの作成](/rest/api/searchservice/create-indexer)」をご覧ください。

インデクサーのスケジュールの定義の詳細については、[Azure Cognitive Search のインデクサーのスケジュールを設定する方法](search-howto-schedule-indexers.md)に関する記事を参照してください。

## <a name="accessing-network-secured-data-in-storage-accounts"></a>ストレージ アカウントのネットワークで保護されたデータにアクセスする

Azure ストレージ アカウントは、ファイアウォールと仮想ネットワークを利用することでさらにセキュリティを強化できます。 ファイアウォールや仮想ネットワークを利用してセキュリティを強化した BLOB ストレージ アカウントまたは Data Lake Gen2 ストレージ アカウントからコンテンツにインデックスを付ける場合、「[信頼されたサービスの例外を使用してストレージ アカウントのデータに安全にアクセスする](search-indexer-howto-access-trusted-service-exception.md)」の指示に従ってください。

## <a name="see-also"></a>関連項目

* [Azure BLOB インデクサー](search-howto-indexing-azure-blob-storage.md)
* [Azure Data Lake Storage Gen2 インデクサー](search-howto-index-azure-data-lake-storage.md)
* [Azure テーブル インデクサー](search-howto-indexing-azure-tables.md)
* [C# 例: Azure AD を使用して Lake Gen2 のデータにインデックスを作成する (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/data-lake-gen2-acl-indexing/README.md)