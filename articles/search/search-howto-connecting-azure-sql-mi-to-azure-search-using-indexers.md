---
title: SQL Managed Instances へのインデクサー接続
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 上のインデクサーから SQL マネージド インスタンスへの接続を許可するために、パブリック エンドポイントを有効にします。
manager: nitinme
author: vl8163264128
ms.author: victliu
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/26/2021
ms.openlocfilehash: 12ee369bfd69e82a73ccaa766190cedf7910a7a0
ms.sourcegitcommit: 3b371af4c30fce82854b3d45fd8b8e674bbe7517
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2021
ms.locfileid: "112984896"
---
# <a name="indexer-connections-to-azure-sql-managed-instance-through-a-public-endpoint"></a>パブリック エンドポイントを介した Azure SQL Managed Instance へのインデクサー接続

Azure SQL マネージド インスタンスに接続する Azure Cognitive Search インデクサーを設定する場合は、前提条件としてマネージド インスタンスでパブリック エンドポイントを有効にする必要があります。 インデクサーのマネージド インスタンスへの接続は、パブリック エンドポイントを介して行われます。

この記事では、データ ソースを構成するために必要な情報の収集を含む基本的な手順について説明します。 詳細情報と方法については、「[Azure SQL Managed Instance のパブリック エンドポイントを構成する](../azure-sql/managed-instance/public-endpoint-configure.md)」を参照してください。

## <a name="enable-a-public-endpoint"></a>パブリック エンドポイントを有効にする

新しい SQL マネージド インスタンスで、 **[パブリック エンドポイントの有効化]** オプションを選択した状態でリソースを作成します。

   ![パブリック エンドポイントを有効にする](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "パブリック エンドポイントの有効化")

または、インスタンスが既に存在する場合は、 **[セキュリティ]**  >  **[仮想ネットワーク]**  >  **[パブリック エンドポイント]**  >  **[有効化]** によって、既存の SQL マネージド インスタンスのパブリック エンドポイントを有効にできます。

   ![マネージド インスタンス VNET を使用してパブリック エンドポイントを有効にする](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "パブリック エンドポイントの有効化")

## <a name="verify-nsg-rules"></a>NSG 規則を確認する

ネットワーク セキュリティ グループに、Azure サービスからの接続を許可する正しい **受信セキュリティ規則** が設定されていることを確認します。

   ![NSG インバウンド セキュリティ規則](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "NSG インバウンド セキュリティ規則")

## <a name="restrict-inbound-access-to-the-endpoint"></a>エンドポイントへの受信アクセスを制限する

現在の規則 (`public_endpoint_inbound`) を次の 2 つの規則に置き換えることによって、パブリック エンドポイントへの受信アクセスを制限できます。

* `AzureCognitiveSearch` [サービス タグ](../virtual-network/service-tags-overview.md#available-service-tags) ("SOURCE" = `AzureCognitiveSearch`、"NAME" = `cognitive_search_inbound`) からの受信アクセスを許可する

* 検索サービスの IP アドレスからの受信アクセスを許可します。これは、完全修飾ドメイン名 (`<your-search-service-name>.search.windows.net` など) を ping することで取得できます。 ("SOURCE" = `IP address`、"NAME" = `search_service_inbound`)

各規則に対して、"PORT" = `3342`、"PROTOCOL" = `TCP`、"DESTINATION" = `Any`、"ACTION" = `Allow` を設定します。

## <a name="get-public-endpoint-connection-string"></a>パブリック エンドポイントの接続文字列を取得する

検索インデクサーのデータ ソース接続内の使用する接続文字列をコピーします。 **パブリック エンドポイント** の接続文字列 (ポート 1433 ではなく、ポート 3342) をコピーしていることを確認します。

   ![パブリック エンドポイントの接続文字列](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "パブリック エンドポイントの接続文字列")

## <a name="next-steps"></a>次のステップ

構成が終わり、[SQL マネージド インスタンスをインデクサー データ ソース](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)として指定できるようになりました。