---
title: インデクサーの IP 範囲へのアクセスを許可する
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search インデクサーによるデータ アクセスを許可するように、IP ファイアウォール規則を構成します。
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: how-to
ms.date: 11/11/2021
ms.openlocfilehash: 50ee624a3795015d35637e0e984a48025c802698
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132492722"
---
# <a name="configure-ip-firewall-rules-to-allow-indexer-connections-in-azure-cognitive-search"></a>Azure Cognitive Search でインデクサー接続を許可するように IP ファイアウォール規則を構成する

インデックスの作成中にデータをプルするには、インデクサーの代わりに、検索サービスによって送信呼び出しが外部の Azure リソースに発行されます。 着信呼び出しをフィルター処理するために、ご利用の Azure リソースで IP ファイアウォール規則が使用されている場合は、インデクサー要求を許可するインバウンド規則を、ファイアウォール内に作成する必要があります。

この記事では、ご利用の検索サービスの IP アドレスを検索してから、Azure portal を使用して Azure Storage アカウント上でインバウンド IP 規則を構成する方法について説明します。 この方法は Azure Storage に固有のものですが、データ アクセスに IP ファイアウォール規則を使用する他の Azure リソース (Cosmos DB や Azure SQL など) でも機能します。

> [!NOTE]
> ストレージ アカウントと検索サービスが互いに異なるリージョンにある場合にのみ、ストレージ アカウント用の IP ファイアウォール規則は有効です。 これがセットアップで許可されていない場合は、代替手段として[信頼されたサービスの例外オプション](search-indexer-howto-access-trusted-service-exception.md)を利用することをお勧めします。

## <a name="get-a-search-service-ip-address"></a>検索サービスの IP アドレスを取得する

1. 使用している検索サービスの完全修飾ドメイン名 (FQDN) を特定します。 これは `<search-service-name>.search.windows.net` のようになります。 FQDN は、Azure portal で検索サービスを調べることで確認できます。

   ![サービスの FQDN を取得する](media\search-indexer-howto-secure-access\search-service-portal.png "サービスの FQDN を取得する")

1. コマンド プロンプトで FQDN の `nslookup` (または `ping`) を実行して、検索サービスの IP アドレスを検索します。

1. 次の手順でインバウンド規則に指定できるように、IP アドレスをコピーします。 次の例では、コピーする必要がある IP アドレスは "150.0.0.1" です。

   ```azurepowershell
   nslookup contoso.search.windows.net
   Server:  server.example.org
   Address:  10.50.10.50
    
   Non-authoritative answer:
   Name:    <name>
   Address:  150.0.0.1
   aliases:  contoso.search.windows.net
   ```

## <a name="get-ip-addresses-for-azurecognitivesearch-service-tag"></a>"AzureCognitiveSearch" サービス タグの IP アドレスを取得する

検索サービスの構成によっては、IP アドレスの範囲からの要求を許可するインバウンド規則を作成することが必要な場合もあります。 具体的には、インデクサーの[マルチテナント実行環境](search-indexer-securing-resources.md#indexer-execution-environment)から発信される要求に、追加の IP アドレスが使用されます。 

この IP アドレス範囲は、`AzureCognitiveSearch` サービス タグから取得できます。

1. [Discovery API](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api) または [ダウンロード可能な JSON ファイル](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)のいずれかを使用して、`AzureCognitiveSearch` サービス タグの IP アドレス範囲を取得します。

1. 検索サービスが Azure パブリック クラウドの場合は、[Azure パブリック JSON ファイル](https://www.microsoft.com/download/details.aspx?id=56519)をダウンロードする必要があります。

   ![JSON ファイルをダウンロードする](media\search-indexer-howto-secure-access\service-tag.png "JSON ファイルをダウンロードする")

1. JSON ファイルから、検索サービスが米国中西部にあることを前提として、マルチテナント インデクサー実行環境の IP アドレスの一覧が次のように表示されます。

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

`/32` IP アドレスの場合は、"/32" を削除します (規則定義では、52.253.133.74/32 は 52.253.133.74 になります)。 他の IP アドレスはすべて逐語的に使用できます。

## <a name="add-ip-addresses-to-ip-firewall-rules"></a>IP ファイアウォール規則に IP アドレスを追加する

IP アドレスを取得したら、規則を設定する準備が整います。 IP アドレス範囲をストレージ アカウントのファイアウォール規則に追加する最も簡単な方法は、Azure portal を使用することです。 

1. ポータルでストレージ アカウントを見つけ、 **[ファイアウォールと仮想ネットワーク]** タブに移動します。

   ![ファイアウォールと仮想ネットワーク](media\search-indexer-howto-secure-access\storage-firewall.png "ファイアウォールと仮想ネットワーク")

1. 事前に取得してある 3 つの IP アドレス (1 つは検索サービスの IP、あとの 2 つは `AzureCognitiveSearch` サービス タグ) をアドレス範囲に追加し、 **[保存]** を選択します。

   ![ファイアウォール IP 規則](media\search-indexer-howto-secure-access\storage-firewall-ip.png "ファイアウォール IP 規則")

ファイアウォール規則が更新されてから、インデクサーがストレージ アカウント内のデータにアクセスできるようになるまで、5 分から 10 分かかる場合があります。

## <a name="next-steps"></a>次の手順

- [Azure Storage ファイアウォールを構成する](../storage/common/storage-network-security.md)
- [Cosmos DB の IP ファイアウォールを構成する](../cosmos-db/how-to-configure-firewall.md)
- [Azure SQL Server の IP ファイアウォールを構成する](../azure-sql/database/firewall-configure.md)
