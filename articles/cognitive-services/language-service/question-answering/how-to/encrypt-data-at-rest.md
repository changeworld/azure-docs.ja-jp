---
title: カスタム質問と回答の保存データの暗号化
titleSuffix: Azure Cognitive Services
description: Microsoft からは Microsoft が管理する暗号化キーが提供されます。また、カスタマー マネージド キー (CMK) と呼ばれている独自のキーで自分の Cognitive Services サブスクリプションを管理することをお客様に許可します。 この記事では、カスタム質問と回答のための保存データの暗号化と、CMK を有効化および管理する方法について説明します。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: egeaney
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 8570a1160eddbed5b372daa529339ea3672482cb
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092333"
---
# <a name="custom-question-answering-encryption-of-data-at-rest"></a>カスタム質問と回答の保存データの暗号化

質問と回答を使うと、クラウドに永続化されるデータが自動的に暗号化されるので、組織のセキュリティとコンプライアンスの目標を達成するのに役立ちます。

## <a name="about-encryption-key-management"></a>暗号化キーの管理について

サブスクリプションでは、Microsoft が管理する暗号化キーが既定で使用されます。 カスタマー マネージド キー (CMK) と呼ばれているユーザー独自のキーを使用してサブスクリプションを管理するオプションもあります。 CMK を使用すると、アクセス制御の作成、ローテーション、無効化、取り消しを、いっそう柔軟に行うことができます。 また、データを保護するために使われる暗号化キーを監査することもできます。 CMK がサブスクリプション用に構成されている場合は、Azure Key Vault で暗号化キーを制御しながら、2 つ目の保護レイヤーを提供する二重暗号化を利用できます。

質問と回答では、[Azure Search からの CMK サポート](../../../../search/search-security-manage-encryption-keys.md)が使用され、Azure 検索インデックスに格納されているデータを暗号化するため、与えられた CMK が自動的に関連付けられます。

> [!IMPORTANT]
> Azure Search サービス リソースは 2019 年 1 月以降に作成されている必要があり、無料 (共有) レベルでは使用できません。 現在 Azure portal では、カスタマー マネージド キーの構成はサポートされていません。

## <a name="enable-customer-managed-keys"></a>カスタマー マネージド キーを有効にする

CMK を有効にするには、次の手順に従います。

1.  カスタム質問と回答が有効になっている言語リソースの **[暗号化]** タブに移動します。
2.  **[カスタマー マネージド キー]** オプションを選択します。 自分の [カスタマー マネージド キー](../../../../storage/common/customer-managed-keys-configure-key-vault.md?tabs=portal)の詳細を入力し、 **[保存]** を選びます。

> [!div class="mx-imgBorder"]
> ![質問と回答 CMK](../media/encrypt-data-at-rest/question-answering-cmk.png)
   
3.  正常に保存されると、Azure Search インデックスに格納されているデータの暗号化に CMK が使用されます。

> [!IMPORTANT]
> ナレッジ ベースを作成する前に、新しい Azure Cognitive Search サービスで CMK を設定することをお勧めします。 既存のナレッジ ベースを使用して言語リソースで CMK を設定した場合、そのナレッジ ベースにアクセスできなくなるおそれがあります。 暗号化されたコンテンツを Azure Cognitive Search で使用する方法の詳細については、[こちら](../../../../search/search-security-manage-encryption-keys.md#work-with-encrypted-content)をご覧ください。

> [!NOTE]
> カスタマー マネージド キーを使用できるようにするには、[Cognitive Services カスタマー マネージド キー要求フォーム](https://aka.ms/cogsvc-cmk)に記入して送信します。

## <a name="regional-availability"></a>リージョン別の提供状況

カスタマー マネージド キーは、すべての Azure Search リージョンで使用できます。

## <a name="encryption-of-data-in-transit"></a>転送中のデータの暗号化

Language Studio ポータルは、ユーザーのブラウザーで実行されます。 どのアクションでも、各 Cognitive Service API への直接呼び出しがトリガーされます。 そのため、質問と回答は転送中のデータに対応しています。

## <a name="next-steps"></a>次のステップ

* [Azure Key Vault 内の CMK を使用した Azure Search での暗号化](../../../../search/search-security-manage-encryption-keys.md)
* [保存データの暗号化](../../../../security/fundamentals/encryption-atrest.md)
* [Azure Key Vault の詳細を確認する](../../../../key-vault/general/overview.md)
