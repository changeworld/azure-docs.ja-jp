---
title: プライベート エンドポイントを使用して Purview へのアクセスをセキュリティで保護する
description: この記事では、Purview アカウントにプライベート エンド ポイントを使用する方法についての大まかな概要を説明します
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/18/2021
ms.openlocfilehash: b65820ab72c0a20953a7e3e9e4a2ee964df51e07
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122831613"
---
# <a name="use-private-endpoints-for-your-azure-purview-account"></a>Azure Purview アカウントにプライベート エンドポイントを使用する

この記事では、Azure Purview に対してプライベート エンドポイントを構成する方法について説明します。

## <a name="conceptual-overview"></a>概念
ご利用の Azure Purview アカウントに [Azure プライベート エンドポイント](../private-link/private-endpoint-overview.md)を使用すると、仮想ネットワーク (VNet) 上のユーザーが Private Link を介してカタログに安全にアクセスできるようになります。 プライベート エンドポイントでは、ご利用の Purview アカウント用の VNet アドレス空間からの IP アドレスが使用されます。 VNet 上のクライアントと Purview アカウントの間のネットワーク トラフィックは、VNet と Microsoft バックボーン ネットワーク上のプライベート リンクを経由して転送されます。 

Azure Purview "_アカウント_" プライベート エンドポイントをデプロイすると、プライベート ネットワーク内から発信された、Azure Purview へのクライアント呼び出しのみを許可することができます。

プライベート ネットワーク接続を使用して Azure Purview Studio に接続するには、"_ポータル_" プライベート エンドポイントをデプロイしてください。

プライベート接続を介して Azure 仮想ネットワーク内の Azure IaaS および PaaS データ ソースとオンプレミスのデータ ソースをスキャンする必要がある場合は、"_インジェスト_" プライベート エンドポイントをデプロイしてください。 この方法を使用すれば、データ ソースから Azure Purview Data Map に流れる自分のメタデータについてネットワーク分離を確実に行うことができます。

:::image type="content" source="media/catalog-private-link/purview-private-link-overview.png" alt-text="プライベート エンドポイントを使用した Azure Purview を示すスクリーンショット。"::: 

## <a name="prerequisites"></a>前提条件

Azure Purview アカウント用のプライベート エンドポイントをデプロイする前に、次の前提条件を確実に満たしてください。

1. アクティブなサブスクリプションが含まれる Azure アカウント。 [アカウントは無料で作成できます。](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
<br>
2. 既存の Azure 仮想ネットワーク。 それがない場合は、新しい [Azure 仮想ネットワーク](../virtual-network/quick-create-portal.md)をデプロイします。
<br>

## <a name="azure-purview-private-endpoint-deployment-scenarios"></a>Azure Purview プライベート エンドポイントのデプロイ シナリオ

プライベート エンドポイントを使用して Azure Purview アカウントのデプロイを実行するには、次の推奨チェックリストを使用します。


|シナリオ  |目標  |
|---------|---------|
|**シナリオ 1** - [ご利用の Purview アカウントにプライベートかつ安全に接続する](./catalog-private-link-account-portal.md)   | プライベート エンドポイントを経由した _Azure Purview Studio_ および Atlas API へのアクセスなど、Azure Purview アカウントへのアクセスを有効にする必要があります ("_アカウント_" および "_ポータル_" のプライベート エンドポイントをデプロイします)。   |
|**シナリオ 2** - [ご利用のデータ ソースをプライベートかつ安全にスキャンする](./catalog-private-link-ingestion.md)  | セルフホステッド統合ランタイムを使用して、仮想ネットワークの背後にあるオンプレミスおよび Azure 内のデータ ソースをスキャンする必要があります ("_インジェスト_" プライベート エンドポイントをデプロイする)。    |
|**シナリオ 3** - [ご利用の Azure Purview に接続して、プライベートかつ安全にデータ ソースをスキャンする](./catalog-private-link-end-to-end.md) |Azure Purview アカウントへのアクセスをプライベート エンドポイント経由のみに制限する必要があります。たとえば、エンドツーエンドのネットワーク分離を保証するセルフホステッド統合ランタイムを使用して、仮想ネットワークの背後にあるオンプレミスおよび Azure 内の Azure Purview Studio、Atlas API、スキャン データ ソースにアクセスします ("_アカウント_"、"_ポータル_"、"_インジェスト_" の各プライベート エンドポイントをデプロイします)。   |

## <a name="support-matrix-for-scanning-data-sources-through-_ingestion_-private-endpoint"></a>"_インジェスト_" プライベート エンドポイントを介してデータ ソースをスキャンするためのマトリックスをサポートする

ご利用の Azure Purview アカウントで "_インジェスト_" プライベート エンドポイントが使用されていて、ご利用のデータ ソースに対するパブリック アクセスが無効になっているシナリオでは、Azure Purview を使用することで、プライベート エンドポイントの背後にある次のデータ ソースをスキャンすることができます。

|プライベート エンドポイントの背後にあるデータ ソース  |統合ランタイムの種類  |資格情報の種類  |
|---------|---------|---------|
|Azure Blob Storage | セルフホステッド IR | サービス プリンシパル|
|Azure Blob Storage | セルフホステッド IR | アカウント キー|
|Azure Data Lake Storage Gen 2 | セルフホステッド IR| サービス プリンシパル|
|Azure Data Lake Storage Gen 2 | セルフホステッド IR| アカウント キー|
|Azure SQL データベース | セルフホステッド IR| SQL 認証|
|Azure SQL データベース | セルフホステッド IR| サービス プリンシパル|
|Azure SQL Managed Instance | セルフホステッド IR| SQL 認証|
|Azure Cosmos DB| セルフホステッド IR| アカウント キー|
|SQL Server | セルフホステッド IR| SQL 認証|
|Azure Synapse Analytics | セルフホステッド IR| サービス プリンシパル|
|Azure Synapse Analytics | セルフホステッド IR| SQL 認証|

## <a name="frequently-asked-questions"></a>よく寄せられる質問  

Azure Purview でのプライベート エンドポイントのデプロイに関連する FAQ については、「[Azure Purview プライベート エンドポイントに関する FAQ](./catalog-private-link-faqs.md)」を参照してください。
 
## <a name="troubleshooting-guide"></a>トラブルシューティング ガイド 
Purview アカウントのプライベート エンドポイント構成のトラブルシューティングについては、「[Purview アカウントのプライベート エンドポイント構成のトラブルシューティング](./catalog-private-link-troubleshoot.md)」を参照してください。

## <a name="known-limitations"></a>既知の制限事項
Azure Purview プライベート エンドポイントに関連する現在の制限事項の一覧を表示するには、[Azure Purview プライベート エンドポイントの既知の制限事項](./catalog-private-link-troubleshoot.md#known-limitations)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

- [インジェスト プライベート エンドポイントをデプロイする](./catalog-private-link-ingestion.md)
