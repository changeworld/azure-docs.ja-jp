---
title: QnA Maker での保存データの暗号化
titleSuffix: Azure Cognitive Services
description: Microsoft からは Microsoft が管理する暗号化キーが提供されます。また、カスタマー マネージド キー (CMK) と呼ばれている独自のキーで自分の Cognitive Services サブスクリプションを管理することをお客様に許可します。 この記事では、QnA Maker での保存データの暗号化と、CMK を有効化および管理する方法について説明します。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: egeaney
ms.custom: ignite-fall-2021
ms.openlocfilehash: 81267ba4c8b1d903e9ca83ff1333a5347c2c1463
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131043758"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>QnA Maker での保存データの暗号化

QnA Maker を使うと、クラウドに永続化されるデータが自動的に暗号化されるので、組織のセキュリティとコンプライアンスの目標を達成するのに役立ちます。

## <a name="about-encryption-key-management"></a>暗号化キーの管理について

サブスクリプションでは、Microsoft が管理する暗号化キーが既定で使用されます。 カスタマー マネージド キー (CMK) と呼ばれているユーザー独自のキーを使用してサブスクリプションを管理するオプションもあります。 CMK を使用すると、アクセス制御の作成、ローテーション、無効化、取り消しを、いっそう柔軟に行うことができます。 また、データを保護するために使われる暗号化キーを監査することもできます。 CMK がサブスクリプション用に構成されている場合は、Azure Key Vault で暗号化キーを制御しながら、2 つ目の保護レイヤーを提供する二重暗号化を利用できます。

QnA Maker では、Azure Search から CMK のサポートが使用されます。 [Azure Key Vault を使用して Azure Search 内で CMK](../../search/search-security-manage-encryption-keys.md) を構成します。 この Azure インスタンスを QnA Maker サービスに関連付けて、CMK を有効にする必要があります。


> [!IMPORTANT]
> Azure Search サービス リソースは 2019 年 1 月以降に作成されている必要があり、無料 (共有) レベルでは使用できません。 現在 Azure portal では、カスタマー マネージド キーの構成はサポートされていません。

## <a name="enable-customer-managed-keys"></a>カスタマー マネージド キーを有効にする

QnA Maker サービスでは、Azure Search サービスから CMK が使用されます。 CMK を有効にするには、次の手順に従います。

1. 新しい Azure Search インスタンスを作成し、[Azure Cognitive Search でのカスタマー マネージド キーの前提条件](../../search/search-security-manage-encryption-keys.md#prerequisites)に関する記事に記載されている前提条件に従います。

   ![暗号化の設定を表示する 1](../media/cognitive-services-encryption/qna-encryption-1.png)

2. QnA Maker リソースを作成すると、Azure Search インスタンスに自動的に関連付けられます。 このインスタンスでは CMK を使用できません。 CMK を使用するには、手順 1 で新しく作成した Azure Search インスタンスを関連付ける必要があります。 具体的には、QnA Maker リソースの `AzureSearchAdminKey` と `AzureSearchName` を更新する必要があります。

   ![暗号化の設定を表示する 2](../media/cognitive-services-encryption/qna-encryption-2.png)

3. 次に、新しいアプリケーション設定を作成します。
   * **名前**:`CustomerManagedEncryptionKeyUrl`
   * **値**: Azure Search インスタンスを作成する手順 1 で取得した値を使用します。

   ![暗号化の設定を表示する 3](../media/cognitive-services-encryption/qna-encryption-3.png)

4. 完了したら、ランタイムを再起動します。 これで、QnA Maker サービスで CMK が有効になりました。

## <a name="regional-availability"></a>リージョン別の提供状況

カスタマー マネージド キーは、すべての Azure Search リージョンで使用できます。

## <a name="encryption-of-data-in-transit"></a>転送中のデータの暗号化

QnA Maker ポータルは、ユーザーのブラウザー上で実行されます。 どのアクションでも、各 Cognitive Service API への直接呼び出しがトリガーされます。 そのため、QnA Maker では転送中のデータに対応しています。
ただし、QnA Maker ポータル サービスは米国西部でホストされており、まだ米国以外のお客様にとっては最適な状況ではありません。 

## <a name="next-steps"></a>次のステップ

* [Azure Key Vault 内の CMK を使用した Azure Search での暗号化](../../search/search-security-manage-encryption-keys.md)
* [保存データの暗号化](../../security/fundamentals/encryption-atrest.md)
* [Azure Key Vault の詳細を確認する](../../key-vault/general/overview.md)
