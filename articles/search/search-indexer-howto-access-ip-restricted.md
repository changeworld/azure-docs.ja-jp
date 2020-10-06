---
title: インデクサーの IP 範囲へのアクセスを許可する
titleSuffix: Azure Cognitive Search
description: インデクサーによるアクセスが可能なように IP ファイアウォール規則を設定する方法について説明するハウツー ガイド。
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: c80462707d3dccbb8fccff244017053c25ad46e8
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2020
ms.locfileid: "89463304"
---
# <a name="setting-up-ip-firewall-rules-to-enable-indexer-access"></a>インデクサーによるアクセスを可能にする IP ファイアウォール規則の設定

ストレージ アカウント、Cosmos DB アカウント、Azure SQL サーバーなどの Azure リソースに関する IP ファイアウォール規則では、特定の IP 範囲を送信元とするトラフィックにのみデータへのアクセスが許可されます。

この記事では、Azure portal を使用してストレージ アカウントの IP ルールを構成して、Azure Cognitive Search インデクサーによるデータへのアクセスをセキュリティで保護できるようにする方法について説明します。 このガイドはストレージに固有のものですが、データへのアクセスをセキュリティで保護するために IP ファイアウォール規則も提供する他の Azure リソースでそのまま使用できます。

> [!NOTE]
> ストレージ アカウントの IP ファイアウォール規則は、ストレージ アカウントと検索サービスが異なるリージョンにある場合にのみ効力を発揮します。 設定でこれが許可されない場合は、[信頼されたサービスの例外オプション](search-indexer-howto-access-trusted-service-exception.md)を使用することをお勧めします。

## <a name="get-the-ip-address-of-the-search-service"></a>検索サーバーの IP アドレスを取得する

お使いの検索サービスの完全修飾ドメイン名 (FQDN) を取得します。 これは `<search-service-name>.search.windows.net` のようになります。 FQDN は、Azure portal で検索サービスを調べることで確認できます。

   ![サービスの FQDN を取得する](media\search-indexer-howto-secure-access\search-service-portal.png "サービスの FQDN を取得する")

検索サービスの IP アドレスは、FQDN の `nslookup` (または `ping`) を実行することで取得できます。 これはファイアウォール規則に追加する IP アドレスの 1 つになります。

```azurepowershell

nslookup contoso.search.windows.net
Server:  server.example.org
Address:  10.50.10.50

Non-authoritative answer:
Name:    <name>
Address:  150.0.0.1
Aliases:  contoso.search.windows.net
```

## <a name="get-the-ip-address-ranges-for-azurecognitivesearch-service-tag"></a>"AzureCognitiveSearch" サービス タグの IP アドレス範囲を取得する

`AzureCognitiveSearch` サービス タグの IP アドレス範囲は、[Discovery API (プレビュー)](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) または[ダウンロード可能な JSON ファイル](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)経由で取得できます。

このチュートリアルでは、検索サービスが Azure パブリック クラウドであることを前提として、[Azure パブリック JSON ファイル](https://www.microsoft.com/download/details.aspx?id=56519)をダウンロードする必要があります。

   ![JSON ファイルをダウンロードする](media\search-indexer-howto-secure-access\service-tag.png "JSON ファイルをダウンロードする")

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

/32 IP アドレスの場合は "/32" (52.253.133.74/32 -> 52.253.133.74) をドロップします。その他は逐語的に使用できます。

## <a name="add-the-ip-address-ranges-to-ip-firewall-rules"></a>IP アドレスを IP ファイアウォール規則に追加する

IP アドレス範囲をストレージ アカウントのファイアウォール規則に追加する最も簡単な方法は、Azure portal を使用することです。 ポータルでストレージ アカウントを見つけ、 **[ファイアウォールと仮想ネットワーク]** タブに移動します。

   ![ファイアウォールと仮想ネットワーク](media\search-indexer-howto-secure-access\storage-firewall.png "ファイアウォールと仮想ネットワーク")

前もって取得した 3 つの IP アドレス (1 つは検索サービスの IP、2 つは `AzureCognitiveSearch` サービス タグ) をアドレス範囲に追加し、 **[保存]** をクリックします。

   ![ファイアウォール IP 規則](media\search-indexer-howto-secure-access\storage-firewall-ip.png "ファイアウォール IP 規則")

ファイアウォール規則は更新されるまで 5 分から 10 分かかります。その後、インデクサーでストレージ アカウントのデータにアクセスできるようになります。

## <a name="next-steps"></a>次の手順

インデックスへのアクセスを許可するために 2 つの IP アドレス セットを取得する方法がわかったので、次のリンクを使用して、いくつかの一般的なデータ ソースに対する IP ファイアウォール規則を更新します。

- [Azure Storage ファイアウォールを構成する](https://docs.microsoft.com/azure/storage/common/storage-network-security)
- [CosmosDB の IP ファイアウォールを構成する](https://docs.microsoft.com/azure/cosmos-db/firewall-support)
- [Azure SQL Server の IP ファイアウォールを構成する](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)