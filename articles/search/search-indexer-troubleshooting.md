---
title: 検索インデクサーの一般的な問題のトラブルシューティング
titleSuffix: Azure Cognitive Search
description: データ ソース接続、ファイアウォール、および不足しているドキュメントなど、Azure Cognitive Search でのインデクサーのエラーと一般的な問題を修正。
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: efdd9666c8876ddaf12b9555fa66beb62c56e93e
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107740065"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>Azure Cognitive Search のインデクサーの一般的な問題のトラブルシューティング

Azure Cognitive Search においてデータにインデックスを付けるとき、インデクサーでいくつかの問題が発生することがあります。 エラーは次のように分類されます。

* [データ ソースまたはその他のリソースへの接続](#connection-errors)
* [ドキュメントの処理](#document-processing-errors)
* [インデックスへのドキュメントの取り込み](#index-errors)

## <a name="connection-errors"></a>接続エラー

> [!NOTE]
> インデクサーでは、Azure ネットワーク セキュリティ メカニズムによって保護されているデータ ソースやその他のリソースへのアクセスに対するサポートが制限されています。 現時点では、対応する IP アドレス範囲の制限メカニズムまたは NSG 規則 (該当する場合) を通じてのみデータ ソースにアクセスできます。 サポートされている各データ ソースへのアクセス方法の詳細については、以下を参照してください。
>
> 検索サービスの IP アドレスを確認するには、その完全修飾ドメイン名 (例: `<your-search-service-name>.search.windows.net`) を ping します。
>
> `AzureCognitiveSearch` [サービス タグ](../virtual-network/service-tags-overview.md#available-service-tags)の IP アドレス範囲を確認するには、[ダウンロード可能な JSON ファイル](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)または [Service Tag Discovery API](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) を使用します。 IP アドレス範囲は毎週更新されます。

### <a name="configure-firewall-rules"></a>ファイアウォール規則を構成する

Azure Storage、CosmosDB、および Azure SQL では、構成可能なファイアウォールが提供されます。 ファイアウォールが有効になっているとき、特定のエラー メッセージはありません。 通常、ファイアウォールのエラーは一般的であり、`The remote server returned an error: (403) Forbidden` や `Credentials provided in the connection string are invalid or have expired` のようなものです。

こうした場合にインデクサーがこれらのリソースにアクセスできるようにするには、次の 2 つのオプションがあります。

* **[すべてのネットワーク]** からのアクセスを許可して、ファイアウォールを無効にします (可能な場合)。
* または、リソースのファイアウォール規則 (IP アドレス範囲の制限) で、検索サービスの IP アドレスと `AzureCognitiveSearch` [サービス タグ](../virtual-network/service-tags-overview.md#available-service-tags) の IP アドレス範囲に対するアクセスを許可することもできます。

データ ソースの種類ごとに IP アドレス範囲の制限を構成する方法の詳細については、次のリンクを参照してください。

* [Azure ストレージ](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [Cosmos DB](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [Azure SQL](../azure-sql/database/firewall-configure.md#create-and-manage-ip-firewall-rules)

**制限事項**:上記の Azure Storage のドキュメントに記載されているように、IP アドレス範囲の制限は、検索サービスとストレージ アカウントが異なるリージョンにある場合にのみ機能します。

([カスタム Web API スキル](cognitive-search-custom-skill-web-api.md)として使用されている場合がある) Azure 関数でも [IP アドレスの制限](../azure-functions/ip-addresses.md#ip-address-restrictions)がサポートされています。 構成する IP アドレスの一覧は、検索サービスの IP アドレスと `AzureCognitiveSearch` サービス タグの IP アドレス範囲になります。

Azure VM 上の SQL Server のデータへのアクセス方法の詳細については、[こちら](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)で説明しています

### <a name="configure-network-security-group-nsg-rules"></a>ネットワーク セキュリティ グループ (NSG) 規則を構成する

SQL Managed Instance のデータにアクセスする場合、または Azure VM を[カスタム Web API スキル](cognitive-search-custom-skill-web-api.md)の Web サービス URI として使用する場合、お客様は特定の IP アドレスを気にする必要はありません。

このような場合は、Azure VM または SQL Managed Instance を仮想ネットワーク内に配置するように構成できます。 そして、仮想ネットワーク サブネットとネットワーク インターフェイスに出入りできるネットワーク トラフィックの種類をフィルター処理するようにネットワーク セキュリティ グループを構成できます。

`AzureCognitiveSearch` サービス タグは、IP アドレス範囲を検索しなくても、受信 [NSG 規則](../virtual-network/manage-network-security-group.md#work-with-security-rules)で直接使用できます。

SQL Managed Instance 内のデータへのアクセス方法の詳細については、[こちら](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)で説明しています

### <a name="cosmosdb-indexing-isnt-enabled"></a>CosmosDB "インデックス付け" が有効でない

Azure Cognitive Search は、Cosmos DB のインデックス付けに暗黙に依存しています。 Cosmos DB の自動インデックス付けをオフにすると、Azure Cognitive Search から成功状態が返されますが、コンテナーの内容をインデックス付けすることができません。 設定を確認してインデックス付けをオンにする手順については、[Azure Cosmos DB でのインデックス付けの管理](../cosmos-db/how-to-manage-indexing-policy.md#use-the-azure-portal)に関する記事をご覧ください。

### <a name="sharepoint-online-conditional-access-policies"></a>SharePoint Online の条件付きアクセス ポリシー

SharePoint Online インデクサーの作成時には、デバイス コードを指定した後に、AAD アプリにログインすることを求められる手順を実行します。 「サインインは成功しましたが、アクセスを要求しているデバイスを管理者が管理する必要があります」というメッセージが表示された場合は、[条件付きアクセス](https://review.docs.microsoft.com/azure/active-directory/conditional-access/overview) ポリシーにより、インデクサーが SharePoint Online ドキュメント ライブラリにアクセスすることがブロックされている可能性があります。

ドキュメント ライブラリへのインデクサーによるアクセスを許可するようにポリシーを更新するには、以下の手順に従います。

1. Azure portal を開き、「**Azure AD 条件付きアクセス**」を検索し、左側のメニューの **[ポリシー]** を選択します。 このページを表示するためのアクセス権がない場合は、アクセス権を持つ他のユーザーを見つけるか、アクセス権を取得する必要があります。

1. SharePoint Online インデクサーによるドキュメント ライブラリへのアクセスをブロックしているポリシーを特定します。 インデクサーをブロックしている可能性のあるポリシーには、 **[ユーザーとグループ]** セクションでのインデクサーの作成手順で認証に使用したユーザー アカウントが含まれています。 ポリシーには次の **条件** も含まれている場合があります。
    * **Windows** プラットフォームを制限する。
    * **モバイル アプリとデスクトップ クライアント** を制限する。
    * **[デバイスの状態]** が **[はい]** に構成されている。

1. インデクサーをブロックしているポリシーがあることを確認したら、次にインデクサーを除外する必要があります。 検索サービスの IP アドレスを取得します。

    1. お使いの検索サービスの完全修飾ドメイン名 (FQDN) を取得します。 これは `<search-service-name>.search.windows.net` のようになります。 FQDN は、Azure portal で検索サービスを調べることで確認できます。

   ![サービスの FQDN を取得する](media\search-indexer-howto-secure-access\search-service-portal.png "サービスの FQDN を取得する")

    検索サービスの IP アドレスは、FQDN の `nslookup` (または `ping`) を実行することで取得できます。 以下の例では、Azure Storage ファイアウォールのインバウンド規則に "150.0.0.1" を追加します。 検索サービス インデクサーが Azure Storage アカウントにアクセスできるようにするには、ファイアウォール設定が更新されてから最大 15 分かかることがあります。

    ```azurepowershell

    nslookup contoso.search.windows.net
    Server:  server.example.org
    Address:  10.50.10.50
    
    Non-authoritative answer:
    Name:    <name>
    Address:  150.0.0.1
    Aliases:  contoso.search.windows.net
    ```

1. リージョンのインデクサー実行環境の IP アドレスの範囲を取得します。

    追加の IP アドレスは、インデクサーの[マルチテナント実行環境](search-indexer-securing-resources.md#indexer-execution-environment)からの要求に使用されます。 この IP アドレス範囲は、サービス タグから取得できます。

    `AzureCognitiveSearch` サービス タグの IP アドレス範囲は、[Discovery API (プレビュー)](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) または[ダウンロード可能な JSON ファイル](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)経由で取得できます。

    このチュートリアルでは、検索サービスが Azure パブリック クラウドであることを前提として、[Azure パブリック JSON ファイル](https://www.microsoft.com/download/details.aspx?id=56519)をダウンロードする必要があります。

   ![JSON ファイルをダウンロードする](media\search-indexer-troubleshooting\service-tag.png "JSON ファイルをダウンロードする")

    JSON ファイルから、検索サービスが米国中西部にあることを前提として、マルチテナント インデクサー実行環境の IP アドレスの一覧が次のように表示されます。

    ```json
        {
          "name": "AzureCognitiveSearch.WestCentralUS",
          "id": "AzureCognitiveSearch.WestCentralUS",
          "properties": {
            "changeNumber": 1,
            "region": "westcentralus",
            "platform": "Azure",
            "systemService": "AzureCognitiveSearch",
            "addressPrefixes": [
              "52.150.139.0/26",
              "52.253.133.74/32"
            ]
          }
        }
    ```

1. Azure portal の [条件付きアクセス] ページに戻り、左側のメニューで **[ネームド ロケーション]** を選択し、 **[IP 範囲の場所]** を選択します。 新しいネームド ロケーションに名前を付けて、最後の 2 つの手順で収集した検索サービスとインデクサーの実行環境の IP 範囲を追加します。
    * 検索サービスの IP アドレスでは、有効な IP 範囲のみが受け入れられるため、IP アドレスの末尾に "/32" を追加する必要がある場合があります。
    * インデクサーの実行環境の IP 範囲は、検索サービスが存在するリージョンの IP 範囲を追加する必要があるだけであることに注意してください。

1. 新しいネームド ロケーションをポリシーから除外します。 
    1. 左側のメニューの **[ポリシー]** を選択します。 
    1. インデクサーをブロックしているポリシーを選択します。
    1. **[条件]** を選択します。
    1. **[場所]** を選択します。
    1. **[除外]** を選択し、新しいネームド ロケーションを追加します。
    1. 変更を **[保存]** します。

1. ポリシーが更新され、新しいポリシー ルールが適用されるまで数分待ちます。

1. インデクサーを再作成することを試みます。
    1. 作成したデータ ソース オブジェクトに対する更新要求を送信します。
    1. インデクサー作成要求を再送信します。 新しいコードを使用してログインし、ログインが成功したら、別のインデクサー作成要求を送信します。

## <a name="document-processing-errors"></a>ドキュメントの処理エラー

### <a name="unprocessable-or-unsupported-documents"></a>処理できないドキュメントまたはサポートされていないドキュメント

BLOB インデクサーでは、[明示的にサポートされているドキュメント形式が説明](search-howto-indexing-azure-blob-storage.md#SupportedFormats)されています。 場合によっては、サポートされないドキュメントが BLOB ストレージ コンテナーに含まれています。 問題があるドキュメントがあることもあります。 [構成オプションを変更](search-howto-indexing-azure-blob-storage.md#DealingWithErrors)すると、このようなドキュメントに対してインデクサーを停止することを回避できます。

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="missing-document-content"></a>ドキュメントの内容の欠落

BLOB インデクサーによって、[コンテナー内の BLOB からテキストが検索されて抽出](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs)されます。 テキストの抽出に関する問題には次のものがあります。

* ドキュメントに、スキャンしたイメージしか含まれていません。 スキャンしたイメージ (JPG) などテキスト以外のコンテンツを含む PDF BLOB では、標準 BLOB インデックス パイプラインで結果が生成されません。 イメージ コンテンツにテキスト要素が含まれる場合は、[Cognitive Search](cognitive-search-concept-image-scenarios.md) を使用して、テキストを検索して抽出できます。
* BLOB インデクサーは、メタデータのインデックス付けのみを行うように構成されています。 コンテンツを抽出するには、[コンテンツとメタデータの両方を抽出](search-howto-indexing-azure-blob-storage.md#PartsOfBlobToIndex)するように BLOB インデクサーを構成する必要があります。

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="index-errors"></a>インデックスのエラー

### <a name="missing-documents"></a>ドキュメントの欠落

インデクサーによって[データ ソース](/rest/api/searchservice/create-data-source)でドキュメントが検索されます。 場合によっては、インデックスが付けられているはずのデータ ソースのドキュメントが、インデックスに存在しないように見えることがあります。 このようなエラーが発生する一般的な理由がいくつかあります。

* ドキュメントがインデックス付けされていません。 インデクサーの実行が成功したかどうかポータルで確認します。
* [変更追跡](/rest/api/searchservice/create-data-source#data-change-detection-policies)の値を確認します。 高基準値の日付が将来の時刻に設定されている場合、これよりも近い日付になっているドキュメントはすべて、インデクサーによってスキップされます。 [インデクサーの状態](/rest/api/searchservice/get-indexer-status#indexer-execution-result)の 'initialTrackingState' および 'finalTrackingState' フィールドを使用して、インデクサーの変更追跡の状態を把握できます。
* インデクサーの実行後にドキュメントが更新されませんでした。 インデクサーが[スケジュール](/rest/api/searchservice/create-indexer#indexer-schedule)設定されている場合は、いずれ再実行されて、ドキュメントを処理します。
* データ ソースに指定されている[クエリ](/rest/api/searchservice/create-data-source)によって、ドキュメントが除外されています。 インデクサーは、データ ソースに含まれないドキュメントにインデックス付けすることはできません。
* [フィールド マッピング](/rest/api/searchservice/create-indexer#fieldmappings)または [AI エンリッチメント](./cognitive-search-concept-intro.md)によってドキュメントが変更され、予期していたドキュメントとは違って見えます。
* [Lookup Document API](/rest/api/searchservice/lookup-document) を使用してドキュメントを検索します。