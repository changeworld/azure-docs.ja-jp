---
title: "Azure Table Storage のインデックスを Azure Search で作成する"
description: "Azure Search で Azure テーブルに格納されたデータのインデックスを作成する方法について説明します"
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 1cc27411-d0cc-40ed-8aed-c7cb9ab402b9
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 04/10/2017
ms.author: eugenesh
translationtype: Human Translation
ms.sourcegitcommit: 757d6f778774e4439f2c290ef78cbffd2c5cf35e
ms.openlocfilehash: 9b45ab6b86ab0a336b2a4b90e702fa4ff098d41c
ms.lasthandoff: 04/10/2017

---

# <a name="indexing-azure-table-storage-with-azure-search"></a>Azure Table Storage のインデックスを Azure Search で作成する
この記事では、Azure Search を使用して、Azure Table Storage に格納されているデータのインデックスを使用する方法を示します。

## <a name="setting-up-azure-table-indexing"></a>Azure テーブルのインデックス作成の設定

Azure Table インデクサーを設定するには、以下を使用します。

* [Azure ポータル](https://ms.portal.azure.com)
* Azure Search [REST API](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* Azure Search [.NET SDK](https://aka.ms/search-sdk)

ここでは、REST API を使用したフローについて説明します。 

### <a name="step-1-create-a-data-source"></a>手順 1: データ ソースを作成する

データ ソースでは、インデックスを作成するデータ、データにアクセスするために必要な資格情報、および Azure Search がデータの変更を効率よく識別できるようにするポリシーを指定します。

Table インデックス作成の場合は、次のプロパティがデータ ソースに必要です。

- **name** は、Search サービス内のデータ ソースの一意の名前です。
- **type** は `azuretable` である必要があります。
- **credentials** パラメーターは、ストレージ アカウントの接続文字列が含まれます。 詳しくは、「[資格情報を指定する方法](#Credentials)」のセクションをご覧ください。
- **container** はテーブル名とオプションのクエリを設定します。
    - `name` パラメーターを使用して、テーブル名を指定します。
    - 必要に応じて `query` パラメーターを使用して、クエリを指定します。 

> [!IMPORTANT] 
> 可能な場合は、パフォーマンス向上のために PartitionKey でフィルターを使用してください。 その他のクエリはフル テーブル スキャンを実行するため、規模の大きなテーブルではパフォーマンスが下がります。 詳しくは、「[パフォーマンスに関する考慮事項](#Performance)」のセクションをご覧ください。


データ ソースを作成する方法を次に示します。

    POST https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   

データ ソース作成 API の詳細については、「 [データ ソースの作成](https://docs.microsoft.com/rest/api/searchservice/create-data-source)」をご覧ください。

<a name="Credentials"></a>
#### <a name="how-to-specify-credentials"></a>資格情報を指定する方法 ####

次のいずれかの方法でテーブルに対して資格情報を指定できます。 

- **フル アクセス ストレージ アカウントの接続文字列**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>`。 この接続文字列は、ストレージ アカウント ブレードに移動し、[設定]、[キー] と選択する (クラシック ストレージ アカウントの場合) か、[設定]、[アクセス キー] と選択する (Azure Resource Manager ストレージ アカウントの場合) ことで Azure Portal から取得できます。
- **ストレージ アカウントの Shared Access Signature** (SAS) の接続文字列: `TableEndpoint=https://<your account>.table.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=t&sp=rl`。 SAS にはコンテナー (この場合はテーブル) 上およびオブジェクト (テーブル行) にリストおよび読み取りアクセス許可が必要です。
-  **テーブルの Shared Access Signature**: `ContainerSharedAccessUri=https://<your storage account>.table.core.windows.net/<table name>?tn=<table name>&sv=2016-05-31&sig=<the signature>&se=<the validity end time>&sp=r`。 SAS にはテーブル上にクエリ (読み取り) アクセス許可が必要です。

Shared Access Signature について詳しくは、「[Shared Access Signature の使用](../storage/storage-dotnet-shared-access-signature-part-1.md)」をご覧ください。

> [!NOTE]
> SAS の資格情報を使用する場合は、その有効期限が切れないように、データ ソースの資格情報を更新された署名で定期的に更新する必要があります。 SAS の資格情報の有効期限が切れると、インデクサーは「`Credentials provided in the connection string are invalid or have expired.`」のようなエラー メッセージで失敗します。  

### <a name="step-2-create-an-index"></a>手順 2: インデックスを作成する
インデックスでは、検索に使用する、ドキュメント内のフィールド、属性、およびその他の構成要素を指定します。

インデックスを作成する方法を次に示します。

    POST https://[service name].search.windows.net/indexes?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "key", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
          ]
    }

インデックスの作成方法について詳しくは、[インデックスの作成](https://docs.microsoft.com/rest/api/searchservice/create-index)に関する記事をご覧ください。

### <a name="step-3-create-an-indexer"></a>手順 3: インデクサーを作成する
インデクサーはデータ ソースをターゲットの検索インデックスに接続し、データ更新を自動化するスケジュールを提供します。 

インデックスとデータ ソースを作成したら、インデクサーを作成できます。

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

このインデクサーは 2 時間ごとに実行されます (スケジュールの間隔が "PT2H" に設定されています)。 インデクサーを 30 分ごとに実行するには、間隔を "PT30M" に設定します。 サポートされている最短の間隔は 5 分です。 スケジュールは省略可能です。省略した場合、インデクサーは作成時に一度だけ実行されます。 ただし、いつでもオンデマンドでインデクサーを実行できます。   

インデクサー作成 API の詳細については、「 [インデクサーの作成](https://docs.microsoft.com/rest/api/searchservice/create-indexer)」をご覧ください。

## <a name="dealing-with-different-field-names"></a>さまざまなフィールド名の操作
既存のインデックス内のフィールド名が、テーブルのプロパティ名と異なることがあります。 テーブルのプロパティ名は、 **フィールド マッピング** を使用して、検索インデックス内のフィールド名に対応付けることができます。 フィールド マッピングの詳細については、「 [データ ソースと検索インデックスの橋渡し役としての Azure Search インデクサー フィールド マッピング](search-indexer-field-mappings.md)」を参照してください。

## <a name="handling-document-keys"></a>ドキュメント キーの処理
Azure Search では、ドキュメントがそのキーによって一意に識別されます。 それぞれの検索インデックスには、 `Edm.String`型のキー フィールドが 1 つだけ必要です。 キー フィールドは、インデックスに追加するドキュメントごとに必要です (実際のところ、これは唯一の必須フィールドです)。

テーブル行には複合キーがあるため、Azure Search では、パーティション キーと行キーの値が連結された `Key` と呼ばれる合成フィールドが生成されます。 たとえば、行の PartitionKey が `PK1` で、RowKey が `RK1` の場合、`Key` フィールドの値は `PK1RK1` です。

> [!NOTE]
> `Key` 値には、ドキュメント キーでは無効な文字、たとえばダッシュを含めることができます。 無効な文字を扱うには、 `base64Encode` [フィールド マッピング関数](search-indexer-field-mappings.md#base64EncodeFunction)を使用します。 これを行う場合は、Lookup などの API 呼び出しでドキュメント キーを渡す際に、必ず URL の安全な Base64 エンコードを使用する点にも注意してください。
>
>

## <a name="incremental-indexing-and-deletion-detection"></a>インデックスの増分作成と削除の検出
スケジュールに従って実行するようにテーブルのインデクサーを設定すると、行の `Timestamp` 値に従って、新しい行または更新された行のみでインデックスが再作成されます。 変更検出ポリシーを独自に指定する必要はありません。インデックスの増分作成は自動的に有効になります。

一部のドキュメントをインデックスを削除する必要があることを示すには、論理的な削除を使用できます。 行を削除する代わりに、プロパティを追加してそれが削除されていることを示し、データソースに論理的な削除のポリシーを設定します。 たとえば、次のポリシーは値 `"true"` が指定されたプロパティ `IsDeleted` がある行を削除されているものと見なします。

    PUT https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "table name", "query" : "<query>" },
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }   

<a name="Performance"></a>
## <a name="performance-considerations"></a>パフォーマンスに関する考慮事項

既定では、Azure Search は `Timestamp >= HighWaterMarkValue` というクエリ フィルターを使用します。 Azure のテーブルの `Timestamp` フィールドにはセカンダリ インデックスがないため、この種類のクエリはフル テーブル スキャンを必要とし、規模の大きなテーブルでは速度が低下します。


テーブルのインデックス作成のパフォーマンスを向上させる可能性のある 2 つの方法を紹介します。 どちらの方法もテーブルのパーティションを使用します。 

- データを複数のパーティション範囲に自然に分割できる場合は、各パーティション範囲にデータソースと対応するインデクサーを作成します。 これで各インデクサーが特定のパーティション範囲のみを処理するようになるため、クエリのパフォーマンスが向上します。 インデックスを作成する必要があるデータに固定のパーティションが少数ある場合、各インデクサーはパーティションのスキャンのみを実行するため、パフォーマンスがより改善します。 たとえば、キーが `000` から `100` の範囲のパーティションを処理するデータソースを作成するには、次のようなクエリを使用します。 
    ```
    "container" : { "name" : "my-table", "query" : "PartitionKey ge '000' and PartitionKey lt '100' " }
    ```

- データが時間によって分割されている (新しいパーティションを毎日または毎週作成するなどの) 場合は、次の方法を検討してください。 
    - `(PartitionKey ge <TimeStamp>) and (other filters)` の形式のクエリを使用します。 
    - インデクサーの進行状況を [Get Indexer Status API](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) を使用して監視し、直近の成功した高基準値に基づいてクエリの `<TimeStamp>` 条件を定期的に更新します。 
    - この方法では、全インデックスの再作成をトリガーする必要がある場合は、インデクサーのリセットに加えてデータソース クエリをリセットする必要があります。 


## <a name="help-us-make-azure-search-better"></a>Azure Search の品質向上にご協力ください
ご希望の機能や品質向上のアイデアがありましたら、[UserVoice サイト](https://feedback.azure.com/forums/263029-azure-search/)にぜひお寄せください。

