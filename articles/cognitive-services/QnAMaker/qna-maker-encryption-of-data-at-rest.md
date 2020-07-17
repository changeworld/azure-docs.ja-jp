---
title: QnA Maker での保存データの暗号化
titleSuffix: Azure Cognitive Services
description: QnA Maker での保存データの暗号化。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: bd64321a6a7afbac61a63365c77a75120f837e83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371988"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>QnA Maker での保存データの暗号化

QnA Maker を使うと、クラウドに永続化されるデータが自動的に暗号化されるので、組織のセキュリティとコンプライアンスの目標を達成するのに役立ちます。

## <a name="about-encryption-key-management"></a>暗号化キーの管理について

サブスクリプションでは、Microsoft が管理する暗号化キーが既定で使用されます。 ユーザー独自のキーを使用してサブスクリプションを管理するオプションもあります。 カスタマー マネージド キー (CMK) を使用すると、アクセス制御の作成、ローテーション、無効化、取り消しを、いっそう柔軟に行うことができます。 また、データを保護するために使われる暗号化キーを監査することもできます。

QnA Maker では、Azure Search から CMK のサポートが使用されます。 [Azure Key Vault を使用して Azure Search 内に CMK](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys) を作成する必要があります。 この Azure インスタンスを QnA Maker サービスに関連付けて、CMK を有効にする必要があります。

> [!IMPORTANT]
> Azure Search サービス リソースは 2019 年 1 月以降に作成されている必要があり、無料 (共有) レベルでは使用できません。 現在 Azure portal では、カスタマー マネージド キーの構成はサポートされていません。

## <a name="enable-customer-managed-keys"></a>カスタマー マネージド キーを有効にする

QnA Maker サービスでは、Azure Search サービスから CMK が使用されます。 CMK を有効にするには、次の手順に従います。

1. 新しい Azure Search インスタンスを作成し、[Azure Cognitive Search でのカスタマー マネージド キーの前提条件](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys#prerequisites)に関する記事に記載されている前提条件に従います。

   ![暗号化の設定を表示する](../media/cognitive-services-encryption/qna-encryption-1.png)

2. QnA Maker リソースを作成すると、Azure Search インスタンスに自動的に関連付けられます。 これは CMK では使用できません。 CMK を使用するには、手順 1 で新しく作成した Azure Search インスタンスを関連付ける必要があります。 具体的には、QnA Maker リソースの `AzureSearchAdminKey` と `AzureSearchName` を更新する必要があります。

   ![暗号化の設定を表示する](../media/cognitive-services-encryption/qna-encryption-2.png)

3. 次に、新しいアプリケーション設定を作成します。
   * **Name**:これを `CustomerManagedEncryptionKeyUrl`
   * **値**: これは、Azure Search インスタンスを作成する手順 1 で取得した値です。

   ![暗号化の設定を表示する](../media/cognitive-services-encryption/qna-encryption-3.png)

4. 完了したら、ランタイムを再起動します。 これで、QnA Maker サービスで CMK が有効になりました。

## <a name="regional-availability"></a>リージョン別の提供状況

カスタマー マネージド キーは、すべての Azure Search リージョンで使用できます。

## <a name="next-steps"></a>次のステップ

* [Azure Key Vault 内の CMK を使用した Azure Search での暗号化](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)
* [保存データの暗号化](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
* [Azure Key Vault の詳細を確認する](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
