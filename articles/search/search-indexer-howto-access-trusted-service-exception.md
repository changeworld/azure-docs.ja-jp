---
title: 信頼されたサービスの例外を使用した Azure Storage へのインデクサー アクセス
titleSuffix: Azure Cognitive Search
description: Azure Storage に安全に格納されているデータに対して、Azure Cognitive Search のインデクサーによるデータ アクセスを有効にします。
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/11/2021
ms.openlocfilehash: 73512b0c1729fbdbbc62e0063670de8405ddbcb5
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2021
ms.locfileid: "113000286"
---
# <a name="indexer-access-to-azure-storage-using-the-trusted-service-exception-azure-cognitive-search"></a>信頼されたサービスの例外を使用した Azure Storage へのインデクサー アクセス (Azure Cognitive Search)

Azure Storage アカウントのデータにアクセスする Azure Cognitive Search サービスのインデクサーは、[信頼されたサービスの例外](../storage/common/storage-network-security.md#exceptions)機能を利用して、データに安全にアクセスできます。 このメカニズムにより、[IP ファイアウォール規則を使用してインデクサーにアクセス権](search-indexer-howto-access-ip-restricted.md)を付与できないお客様に対して、ストレージ アカウントのデータにアクセスするための簡単、安全かつ無料の代替手段が提供されます。

> [!NOTE]
> 信頼されたサービスの例外を使用してストレージ アカウント内のデータにアクセスするためのサポートは、Azure Blob Storage と Azure Data Lake Gen2 Storage に限定されます。 Azure Table Storage はサポートされていません。

## <a name="step-1-configure-a-connection-using-a-managed-identity"></a>手順 1:マネージド ID を使用して接続を構成する

「[マネージド ID を使用して、Azure Storage アカウントへの接続を設定する](search-howto-managed-identities-storage.md)」の指示に従います。 完了すると、信頼されたサービスとして Azure Active Directory に検索サービスが登録され、 Azure Storage でデータや情報にアクセスする検索 ID 固有の権限を付与するアクセス許可が付与されます。

> [!NOTE]
> この手順では、ポータルを使用して Cognitive Search を信頼されたサービスとして構成する方法について説明します。 これをコードで実現するには、[REST API](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update)、[Azure PowerShell](search-manage-powershell.md#create-a-service-with-a-system-assigned-managed-identity)、または [Azure CLI](search-manage-azure-cli.md#create-a-service-with-a-system-assigned-managed-identity) を使用します。

## <a name="step-2-allow-trusted-microsoft-services-to-access-the-storage-account"></a>手順 2:信頼された Microsoft サービスによるストレージ アカウントへのアクセスを許可する

Azure portal で、ストレージ アカウントの **[ファイアウォールと仮想ネットワーク]** タブに移動します。 **[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]** オプションがチェックされていることを確認します。 このオプションでは、IP ファイアウォール規則によって保護されている場合でも、ストレージ アカウントに対する適切なロールベースのアクセス権を持つ特定の検索サービス インスタンスのみが、ストレージ アカウントのデータへのアクセスを許可されます (強力な認証)。

![信頼されたサービスの例外](media\search-indexer-howto-secure-access\exception.png "信頼されたサービスの例外")

これで、アカウントが IP ファイアウォール規則によって保護されている場合でも、インデクサーはストレージ アカウントのデータにアクセスできます。

## <a name="next-steps"></a>次のステップ

Azure Storage インデクサーの詳細については、以下を参照してください。

- [Azure BLOB インデクサー](search-howto-indexing-azure-blob-storage.md)
- [Azure Data Lake Storage Gen2 インデクサー](search-howto-index-azure-data-lake-storage.md)
- [Azure テーブル インデクサー](search-howto-indexing-azure-tables.md)