---
title: インデクサー リソースに安全にアクセスする
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search のインデクサーが Azure データにアクセスする場合のネットワーク レベルのセキュリティ オプションに関する概念的な概要を示します。
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: 85446847e8ad77bc83eea657ab17268839e0b231
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2020
ms.locfileid: "91949821"
---
# <a name="indexer-access-to-data-sources-using-azure-network-security-features"></a>Azure ネットワーク セキュリティ機能を使用したデータ ソースへのインデクサーのアクセス

Azure Cognitive Search インデクサーは、実行中にさまざまな Azure リソースへの発信呼び出しを行うことができます。 この記事では、リソースが IP ファイアウォール、プライベート エンドポイント、その他のネットワーク レベルのセキュリティ メカニズムによって保護されている場合のそれらのリソースへのインデクサー アクセスの背後にある概念について説明します。 通常の実行でインデクサーがアクセスする可能性があるリソースの種類を次の表に示します。

| リソース | インデクサー実行内の目的 |
| --- | --- |
| Azure Storage (BLOB、テーブル、ADLS Gen 2) | データ ソース |
| Azure Storage (BLOB、テーブル) | スキルセット (エンリッチメントされたドキュメントのキャッシュとナレッジ ストアのプロジェクションの保存) |
| Azure Cosmos DB (さまざまな API) | データ ソース |
| Azure SQL データベース | データ ソース |
| Azure IaaS VM 上の SQL Server | データ ソース |
| SQL マネージド インスタンス | データ ソース |
| Azure Functions | カスタム Web API スキルのホスト |
| Cognitive Services | 20 個の無料ドキュメント制限を超えるエンリッチメントの請求に使用されるスキルセットに関連付けられます |

> [!NOTE]
> スキルセットに関連付けられたコグニティブ サービス リソースは、実行され、検索インデックスに書き込まれたエンリッチメントに基づいて請求に使用されます。 これは、Cognitive Services API にアクセスするためには使用されません。 インデクサーのエンリッチメント パイプラインから Cognitive Services API へのアクセスは、セキュリティで保護された通信チャネルを介して行われます。ここでは、データは転送時に強力に暗号化され、保存されることはありません。

お客様は、Azure が提供する複数のネットワーク分離メカニズムを使用して、これらのリソースをセキュリティで保護できます。 コグニティブ サービス リソースを除いて、インデクサーは、以下の表に概略を示すように、ネットワークが分離されている場合でも、他のすべてのリソースにアクセスする能力に制限があります。

| リソース | IP 制限 | プライベート エンドポイント |
| --- | --- | ---- |
| Azure Storage (BLOB、テーブル、ADLS Gen 2) | ストレージ アカウントと検索サービスが異なるリージョンにある場合にのみサポートされます | サポートされています |
| Azure Cosmos DB - SQL API | サポートされています | サポートされています |
| Azure Cosmos DB - Cassandra、Mongo、Gremlin API | サポートされています | サポートされていない |
| Azure SQL データベース | サポートされています | サポートされています |
| Azure IaaS VM 上の SQL Server | サポートされています | N/A |
| SQL マネージド インスタンス | サポートされています | N/A |
| Azure Functions | サポートされています | Azure Functions の特定の SKU に対してのみサポートされます |

> [!NOTE]
> ネットワークの保護された Azure ストレージ アカウントについては、上記のオプションに加えて、お客様は Azure Cognitive Search が[信頼された Microsoft サービス](../storage/common/storage-network-security.md#trusted-microsoft-services)であるという事実を活用できます。 これは、ストレージ アカウントで適切なロールベースのアクセスの制御が有効になっている場合、特定の検索サービスがストレージ アカウントの仮想ネットワークまたは IP の制限をバイパスし、ストレージ アカウントのデータにアクセスできることを意味します。 詳細については、[ハウツー ガイド](search-indexer-howto-access-trusted-service-exception.md)を参照してください。 このオプションは、ストレージ アカウントまたは検索サービスを別のリージョンに移動できない場合に、IP 制限ルートの代わりに使用できます。

インデクサーで使用するセキュリティ保護されたアクセス メカニズムを選択する場合は、次の制約を考慮してください。

- [サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)は、どの Azure リソースに対してもサポートされません。
- 検索サービスを特定の仮想ネットワークにプロビジョニングすることはできません。この機能は、Azure Cognitive Search では提供されません。
- インデクサーが (発信) プライベート エンドポイントを使用してリソースにアクセスする場合、追加の[プライベート リンク料金](https://azure.microsoft.com/pricing/details/search/)が適用される場合があります。

## <a name="indexer-execution-environment"></a>インデクサー実行環境

Azure Cognitive Search インデクサーは、データ ソースからコンテンツを効率的に抽出し、抽出されたコンテンツにエンリッチメントを追加でき、さらにオプションで、検索インデックスに結果を書き込む前にプロジェクションを生成できます。 インデクサーは、割り当てられている責任の数に応じて、次の 2 つの環境のいずれかで実行できます。

- 特定の検索サービス専用の環境。 このような環境で実行されるインデクサーは、他のワークロード (お客様が開始した他のインデックス作成やクエリのワークロードなど) とリソースを共有します。 通常、この環境では、あまりリソースを必要としない (たとえば、スキルセットを使用しない) インデクサーのみが実行されます。
- 多くのリソースを必要とするインデクサー (スキルセットを使用するものなど) をホストするマルチテナント環境。 多くのリソースを必要とするインデクサーは、この環境で実行されることで、最適なパフォーマンスを提供すると同時に、検索サービスのリソースを他のワークロードで使用できるようにします。 このマルチテナント環境は、Azure Cognitive Search によって管理および保護されており、お客様に追加コストはかかりません。

指定されたインデクサーの実行に対して、Azure Cognitive Search により、そのインデクサーを実行するための最適な環境が決定されます。

## <a name="granting-access-to-indexer-ip-ranges"></a>インデクサーの IP 範囲へのアクセスを許可する

インデクサーがアクセスしようとするリソースが特定の IP 範囲のセットにのみ制限される場合、インデクサー要求が発生する可能性のある IP 範囲を含むようにそのセットを拡張する必要があります。 前述のように、インデクサーが実行され、アクセス要求が発生する可能性がある環境が 2 つあります。 インデクサー アクセスを機能させるために、__両方__の環境の IP アドレスを追加する必要があります。

- 検索サービス固有のプライベート環境の IP アドレスを取得するには、検索サービスの完全修飾ドメイン名 (FQDN) を `nslookup` (または `ping`) します。 たとえば、パブリック クラウドの検索サービスの FQDN は、`<service-name>.search.windows.net` などです。 この情報は、Azure portal で入手できます。
- マルチテナント環境の IP アドレスは、`AzureCognitiveSearch` サービス タグを介して入手できます。 [Azure サービス タグ](../virtual-network/service-tags-overview.md)には、各サービスの公開された IP アドレスの範囲があり、これは [Discovery API (プレビュー)](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) または[ダウンロード可能な JSON ファイル](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)を介して入手できます。 どちらの場合も、IP 範囲はリージョンごとに分類されます。検索サービスがプロビジョニングされているリージョンに割り当てられた IP 範囲のみを選択できます。

特定のデータ ソースについては、IP 範囲の一覧を列挙する代わりに、サービス タグ自体を直接使用できます (検索サービスの IP アドレスは、引き続き明示的に使用する必要があります)。 これらのデータ ソースでは、[ネットワーク セキュリティ グループの規則](../virtual-network/network-security-groups-overview.md)を設定することでアクセスを制限します。これは、Azure Storage、CosmosDB、Azure SQL などが提供する IP 規則とは異なり、サービス タグの追加をネイティブでサポートします。検索サービスの IP アドレスに加えて、`AzureCognitiveSearch` サービス タグを直接使用する機能をサポートするデータ ソースを次に示します。

- [IaaS VM 上の SQL Server](./search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md#restrict-access-to-the-azure-cognitive-search)

- [SQL マネージド インスタンス](./search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md#verify-nsg-rules)

詳細については、[ハウツー ガイド](search-indexer-howto-access-ip-restricted.md)を参照してください。

## <a name="granting-access-via-private-endpoints"></a>プライベート エンドポイントを介してアクセスを許可する

インデクサーは、[プライベート エンドポイント](../private-link/private-endpoint-overview.md)を使用してリソースにアクセスできます (それへのアクセスは、仮想ネットワークを選択するためにロックダウンされているか、あるいはパブリック アクセスが有効になっていません)。
この機能は有料サービスでのみ使用でき、作成されるプライベート エンドポイントの数には制限があります。 この制限の詳細については、[Azure Search の制限](search-limits-quotas-capacity.md)に関するページを参照してください。

### <a name="step-1-create-a-private-endpoint-to-the-secure-resource"></a>手順 1:セキュリティで保護されたリソースへのプライベート エンドポイントを作成する

お客様は、セキュリティで保護されたリソース (たとえば、ストレージ アカウントなど) へのプライベート エンドポイント接続を作成するために、検索管理操作を呼び出し、["*共有プライベート リンク リソース*" API を作成または更新](/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate)する必要があります。 この (発信) プライベート エンドポイント接続を介して移動するトラフィックは、検索サービス固有の "プライベート" インデクサー実行環境にある仮想ネットワークからのみ発信されます。

この API の呼び出し元に、セキュリティで保護されたリソースへのプライベート エンドポイント接続要求を承認するアクセス許可があるかどうかが、Azure Cognitive Search によって検証されます。 たとえば、アクセス権のないストレージ アカウントへのプライベート エンドポイント接続を要求した場合、この呼び出しは拒否されます。

### <a name="step-2-approve-the-private-endpoint-connection"></a>手順 2:プライベート エンドポイント接続を承認する

共有プライベート リンク リソースを作成する (非同期) 操作が完了すると、プライベート エンドポイント接続が "保留中" 状態で作成されます。 この接続を介してフローするトラフィックはまだありません。
お客様は、セキュリティで保護されたリソースでこの要求を見つけて、"承認" することが求められます。 通常、これを行うには、ポータルを使用するか、[REST API](/rest/api/virtualnetwork/privatelinkservices/updateprivateendpointconnection) を使用します。

### <a name="step-3-force-indexers-to-run-in-the-private-environment"></a>手順 3:"プライベート" 環境でインデクサーを強制的に実行する

承認されたプライベート エンドポイントにより、検索サービスから、何らかの形式のネットワーク レベルのアクセス制限があるリソース (たとえば、特定の仮想ネットワークからのみアクセスされるように構成されたストレージ アカウントのデータ ソース) への発信呼び出しを正常に機能させることができます。
これは、プライベート エンドポイントを介してこのようなデータソースに接続できるすべてのインデクサーが正常に機能することを意味します。
プライベート エンドポイントが承認されていない場合、またはインデクサーがプライベート エンドポイント接続を使用していない場合、インデクサーの実行は `transientFailure` で終了します。

インデクサーがプライベート エンドポイント接続を介してリソースにアクセスできるようにするには、インデクサーの `executionEnvironment` を `"Private"` に設定して、すべてのインデクサーの実行でプライベート エンドポイントを使用できるようにする必要があります。 これは、プライベート エンドポイントがプライベート検索サービス固有の環境内でプロビジョニングされるためです。

```json
    {
      "name" : "myindexer",
      ... other indexer properties
      "parameters" : {
          ... other parameters
          "configuration" : {
            ... other configuration properties
            "executionEnvironment": "Private"
          }
        }
    }
```

これらの手順の詳細については、[ハウツー ガイド](search-indexer-howto-access-private.md)を参照してください。
リソースに対して承認されたプライベート エンドポイントを使用すると、*private* に設定されているインデクサーは、プライベート エンドポイント接続を介してアクセスを取得しようとします。

### <a name="limits"></a>制限

検索サービスの最適なパフォーマンスと安定性を確保するために、次のディメンションに対して (検索サービス SKU によって) 制限が適用されます。

- *private* に設定できるインデクサーの種類。
- 作成できる共有プライベート リンク リソースの数。
- 共有プライベート リンク リソースを作成できる個別のリソースの種類の数。

これらの制限は、[サービスの制限](search-limits-quotas-capacity.md)に関する記事で文書化されています。