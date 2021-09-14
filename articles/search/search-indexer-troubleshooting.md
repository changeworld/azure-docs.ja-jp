---
title: インデクサーのトラブルシューティングのガイダンス
titleSuffix: Azure Cognitive Search
description: この記事では、サービス検索からエラー メッセージが返されない場合のインデクサーの問題と解決のガイダンスを提供します。
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/07/2021
ms.openlocfilehash: 650f5f40bf8b8fc0909b4fec85ef6b5724a2e3c7
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/07/2021
ms.locfileid: "123539826"
---
# <a name="indexer-troubleshooting-guidance-for-azure-cognitive-search"></a>Azure Cognitive Search のインデクサーの一般的な問題のトラブルシューティング

インデクサーで問題が発生したが、診断に役立つエラーがない場合があります。 この記事では、インデクサーの結果が予想外であり、先に進むための情報が限られている場合の問題と考えられる解決策について説明します。 調査するエラーがある場合は、「[インデクサーの一般的なエラーと警告のトラブルシューティング](cognitive-search-common-errors-warnings.md)」を参照してください。

## <a name="connection-errors"></a>接続エラー

> [!NOTE]
> インデクサーでは、Azure ネットワーク セキュリティ メカニズムによって保護されているデータ ソースやその他のリソースへのアクセスに対するサポートが制限されています。 現時点では、対応する IP アドレス範囲の制限メカニズムまたは NSG 規則 (該当する場合) を通じてのみデータ ソースにアクセスできます。 サポートされている各データ ソースへのアクセス方法の詳細については、以下を参照してください。
>
> 検索サービスの IP アドレスを確認するには、その完全修飾ドメイン名 (例: `<your-search-service-name>.search.windows.net`) を ping します。
>
> `AzureCognitiveSearch` [サービス タグ](../virtual-network/service-tags-overview.md#available-service-tags)の IP アドレス範囲を確認するには、[ダウンロード可能な JSON ファイル](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)または [Service Tag Discovery API](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) を使用します。 IP アドレス範囲は毎週更新されます。

### <a name="firewall-rules"></a>ファイアウォール規則

Azure Storage、Cosmos DB、および Azure SQL では、構成可能なファイアウォールが提供されます。 ファイアウォールが有効になっているとき、特定のエラー メッセージはありません。 通常、ファイアウォールのエラーは一般的であり、`The remote server returned an error: (403) Forbidden` や `Credentials provided in the connection string are invalid or have expired` のようなものです。

こうした場合にインデクサーでこれらのリソースにアクセスできるようにするには、次の 2 つのオプションがあります。

* **[すべてのネットワーク]** からのアクセスを許可して、ファイアウォールを無効にします (可能な場合)。

* または、リソースのファイアウォール規則 (IP アドレス範囲の制限) で、検索サービスの IP アドレスと `AzureCognitiveSearch` [サービス タグ](../virtual-network/service-tags-overview.md#available-service-tags) の IP アドレス範囲に対するアクセスを許可することもできます。

データ ソースの種類ごとに IP アドレス範囲の制限を構成する方法の詳細については、次のリンクを参照してください。

* [Azure ストレージ](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [Cosmos DB](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [Azure SQL](../azure-sql/database/firewall-configure.md#create-and-manage-ip-firewall-rules)

**制限事項**:上記の Azure Storage のドキュメントに記載されているように、IP アドレス範囲の制限は、検索サービスとストレージ アカウントが異なるリージョンにある場合にのみ機能します。

([カスタム Web API スキル](cognitive-search-custom-skill-web-api.md)として使用されている場合がある) Azure 関数でも [IP アドレスの制限](../azure-functions/ip-addresses.md#ip-address-restrictions)がサポートされています。 構成する IP アドレスの一覧は、検索サービスの IP アドレスと `AzureCognitiveSearch` サービス タグの IP アドレス範囲になります。

仮想マシンへの接続の詳細については、[Azure VM での SQL Server への接続の構成](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)に関する記事を参照してください。

### <a name="configure-network-security-group-nsg-rules"></a>ネットワーク セキュリティ グループ (NSG) 規則を構成する

SQL Managed Instance のデータにアクセスする場合、または Azure VM を[カスタム Web API スキル](cognitive-search-custom-skill-web-api.md)の Web サービス URI として使用する場合、お客様は特定の IP アドレスを気にする必要はありません。

このような場合は、Azure VM または SQL Managed Instance を仮想ネットワーク内に配置するように構成できます。 そして、仮想ネットワーク サブネットとネットワーク インターフェイスに出入りできるネットワーク トラフィックの種類をフィルター処理するようにネットワーク セキュリティ グループを構成できます。

`AzureCognitiveSearch` サービス タグは、IP アドレス範囲を検索しなくても、受信 [NSG 規則](../virtual-network/manage-network-security-group.md#work-with-security-rules)で直接使用できます。

SQL Managed Instance 内のデータへのアクセス方法の詳細については、[こちら](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)で説明しています

## <a name="azure-sql-database-serverless-indexing-error-code-40613"></a>Azure SQL Database のサーバーレス インデックス作成 (エラー コード 40613)

SQL データベースが[サーバーレスのコンピューティング層](../azure-sql/database/serverless-tier-overview.md)にある場合は、インデクサーが接続しているときに、データベースが実行中であり、一時停止されていないことを確認します。

データベースが一時停止されている場合、検索サービスからの最初のログインによってデータベースが自動的に再開されますが、エラー コード 40613 でデータベースを使用できないことを示すエラーが返されます。 データベースが実行されたら、ログインを再試行して接続を確立します。

## <a name="sharepoint-online-conditional-access-policies"></a>SharePoint Online の条件付きアクセス ポリシー

SharePoint Online インデクサーの作成時には、デバイス コードを指定した後に、Azure AD アプリにサインインすることを求められる手順を実行します。 `"Your sign-in was successful but your admin requires the device requesting access to be managed"` (サインインは成功したが、アクセスを要求しているデバイスは管理者によって管理される必要がある) というメッセージが表示された場合は、[条件付きアクセス](../active-directory/conditional-access/overview.md) ポリシーにより、インデクサーの SharePoint Online ドキュメント ライブラリへのアクセスがブロックされている可能性があります。

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
    1. インデクサー作成要求を再送信します。 新しいコードを使用してサインインした後、別のインデクサー作成要求を送信します。

## <a name="indexing-unsupported-document-types"></a>サポートされていないドキュメントの種類のインデックス作成

Azure Blob Storage のコンテンツのインデックスを作成するときに、[サポートされていないコンテンツの種類](search-howto-indexing-azure-blob-storage.md#SupportedFormats)の BLOB がコンテナーに含まれている場合、インデクサーではそのドキュメントはスキップされます。 それ以外の場合、個々のドキュメントに問題がある可能性があります。 

個々のドキュメントに問題がある場合にインデクサーの処理を続行できるようにする[構成オプションを設定](search-howto-indexing-azure-blob-storage.md#DealingWithErrors)できます。

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

## <a name="missing-documents"></a>ドキュメントの欠落

インデクサーでは、外部 [データ ソース](/rest/api/searchservice/create-data-source)からドキュメントまたは行が抽出され、検索サービスによってインデックス作成される *検索ドキュメント* が作成されます。 場合によっては、データ ソースに存在するドキュメントが検索インデックスに表示されません。 この予想外の結果は、次の理由で発生する可能性があります。

* インデクサーの実行後に、ドキュメントが更新された。 インデクサーが[スケジュール](/rest/api/searchservice/create-indexer#indexer-schedule)設定されている場合は、いずれ再実行されて、ドキュメントを処理します。
* ドキュメントを取り込む前にインデクサーがタイムアウトした。 それを超えるとドキュメントが処理されなくなる[最大処理時間制限](search-limits-quotas-capacity.md#indexer-limits)があります。 インデクサーの状態は、ポータルで確認するか、[Get Indexer Status (REST API)](/rest/api/searchservice/get-indexer-status) を呼び出すことで確認できます。
* [フィールド マッピング](/rest/api/searchservice/create-indexer#fieldmappings)または [AI エンリッチメント](./cognitive-search-concept-intro.md)によってドキュメントが変更され、検索インデックス内のそのアーティキュレーションが予想とは異なっている。
* [変更の追跡](/rest/api/searchservice/create-data-source#data-change-detection-policies)の値が間違っている、または前提条件が満たされていない。 高基準値の日付が将来の時刻に設定されている場合、これよりも近い日付になっているドキュメントはすべて、インデクサーによってスキップされます。 [インデクサーの状態](/rest/api/searchservice/get-indexer-status#indexer-execution-result)の 'initialTrackingState' および 'finalTrackingState' フィールドを使用して、インデクサーの変更追跡の状態を把握できます。 Azure SQL と MySQL のインデクサーには、ソース テーブルの高いウォーター マーク列のインデックスが必要です。または、インデクサーによって使用されているクエリがタイム アウトしている可能性があります。 

> [!TIP]
> ドキュメントが見つからない場合は、使用している[クエリ](/rest/api/searchservice/search-documents)を調べて、問題のドキュメントが除外されていないことを確認します。 特定のドキュメントのクエリを実行するには、[Lookup Document REST API](/rest/api/searchservice/lookup-document) を使用します。

## <a name="missing-content-from-blob-storage"></a>Blob Storage のコンテンツが見つからない

BLOB インデクサーによって、[コンテナー内の BLOB からテキストが検索されて抽出](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs)されます。 テキストの抽出に関する問題には次のものがあります。

* ドキュメントに、スキャンしたイメージしか含まれていません。 スキャンしたイメージ (JPG) などテキスト以外のコンテンツを含む PDF BLOB では、標準 BLOB インデックス パイプラインで結果が生成されません。 イメージ コンテンツにテキスト要素が含まれる場合は、[Cognitive Search](cognitive-search-concept-image-scenarios.md) を使用して、テキストを検索して抽出できます。

* BLOB インデクサーは、メタデータのインデックス付けのみを行うように構成されています。 コンテンツを抽出するには、[コンテンツとメタデータの両方を抽出](search-howto-indexing-azure-blob-storage.md#PartsOfBlobToIndex)するように BLOB インデクサーを構成する必要があります。

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="missing-content-from-cosmos-db"></a>Cosmos DB のコンテンツが見つからない

Azure Cognitive Search は、Cosmos DB のインデックス付けに暗黙に依存しています。 Cosmos DB の自動インデックス付けをオフにすると、Azure Cognitive Search から成功状態が返されますが、コンテナーの内容をインデックス付けすることができません。 設定を確認してインデックス付けをオンにする手順については、[Azure Cosmos DB でのインデックス付けの管理](../cosmos-db/how-to-manage-indexing-policy.md#use-the-azure-portal)に関する記事をご覧ください。

## <a name="see-also"></a>関連項目

* [インデクサーの一般的なエラーと警告のトラブルシューティング](cognitive-search-common-errors-warnings.md)
* [インデクサーベースのインデックス作成を監視する](search-howto-monitor-indexers.md)