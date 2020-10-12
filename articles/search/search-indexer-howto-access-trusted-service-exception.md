---
title: 信頼されたサービスの例外によるストレージへのアクセスを許可する
titleSuffix: Azure Cognitive Search
description: ストレージ アカウントのデータに安全にアクセスするために、信頼されたサービスの例外を設定する方法について説明するハウツー ガイド。
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 30fc71e6f59766a759cdb8e4e503123623f48bd9
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2020
ms.locfileid: "91320474"
---
# <a name="accessing-data-in-storage-accounts-securely-via-trusted-service-exception"></a>信頼されたサービスの例外を使用してストレージ アカウントのデータに安全にアクセスする

ストレージ アカウントのデータにアクセスするインデクサーは、[信頼されたサービスの例外](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)機能を利用して、データに安全にアクセスできます。 このメカニズムにより、[IP ファイアウォール規則を使用してインデクサーにアクセス権](search-indexer-howto-access-ip-restricted.md)を付与できないお客様に対して、ストレージ アカウントのデータにアクセスするための簡単、安全かつ無料の代替手段が提供されます。

> [!NOTE]
> 信頼されたサービスの例外を使用してストレージ アカウント内のデータにアクセスするためのサポートは、Azure Blob Storage と Azure Data Lake Gen2 Storage に限定されます。 Azure Table Storage はサポートされていません。

## <a name="step-1-configure-connection-to-the-storage-account-via-identity"></a>手順 1:ID を使用したストレージ アカウントへの接続を構成する

[マネージド ID アクセス ガイド](search-howto-managed-identities-storage.md)に記載されている詳細に従って、検索サービスのマネージド ID を使用してストレージ アカウントにアクセスするようにインデクサーを構成します。

## <a name="step-2-allow-trusted-microsoft-services-to-access-the-storage-account"></a>手順 2:信頼された Microsoft サービスによるストレージ アカウントへのアクセスを許可する

Azure portal で、ストレージ アカウントの [ファイアウォールと仮想ネットワーク] タブに移動します。 [信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します] オプションがチェックされていることを確認します。 このオプションでは、IP ファイアウォール規則によって保護されている場合でも、ストレージ アカウントに対する適切なロールベースのアクセス権を持つ特定の検索サービス インスタンスのみが、ストレージ アカウントのデータへのアクセスを許可されます (強力な認証)。

![信頼されたサービスの例外](media\search-indexer-howto-secure-access\exception.png "信頼されたサービスの例外")

これで、アカウントが IP ファイアウォール規則によって保護されている場合でも、インデクサーはストレージ アカウントのデータにアクセスできます。

## <a name="next-steps"></a>次のステップ

Azure Storage インデクサーの詳細については、以下を参照してください。

- [Azure BLOB インデクサー](search-howto-indexing-azure-blob-storage.md)
- [Azure Data Lake Storage Gen2 インデクサー](search-howto-index-azure-data-lake-storage.md)
- [Azure テーブル インデクサー](search-howto-indexing-azure-tables.md)
