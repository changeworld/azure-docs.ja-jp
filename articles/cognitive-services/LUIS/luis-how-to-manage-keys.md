---
title: LUIS でエンドポイント キーを管理する | Microsoft Docs
description: Language Understanding (LUIS) を使用して、お使いのプログラム API、エンドポイント、および外部キーを管理します。
titleSuffix: Azure
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/21/2018
ms.author: diberry
ms.openlocfilehash: 127c09a022f5efb95ab6a5ec2db0de633b437a54
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223040"
---
# <a name="manage-your-luis-endpoint-keys"></a>LUIS エンドポイント キーの管理
キーを使用すると、ご自身の LUIS アプリを作成して公開するか、お使いのエンドポイントに対してクエリを実行できます。 

<a name="programmatic-key" ></a>
<a name="authoring-key" ></a>
<a name="endpoint-key" ></a>
<a name="use-endpoint-key-in-query" ></a>
<a name="api-usage-of-ocp-apim-subscription-key" ></a>
<a name="key-limits" ></a>
<a name="key-limit-errors" ></a>
## <a name="key-concepts"></a>主要な概念
LUIS のオーサリングおよびエンドポイント キーの概念については、「[LUIS でのキー](luis-concept-keys.md)」を参照してください。

<a name="create-and-use-an-endpoint-key"></a>
## <a name="assign-endpoint-key"></a>エンドポイント キーの割り当て
**[Publish app]\(アプリの公開\)** ページには、**[Resources and Keys]\(リソースとキー\)** テーブルに既にキーがあります。 これは、オーサリング (スターター) キーです。 

1. [Azure portal](https://portal.azure.com) で LUIS キーを作成します。 詳細な手順については、[Azure を使用したエンドポイント キーの作成](luis-how-to-azure-subscription.md)に関するページをご覧ください。
 
2. 前の手順で作成した LUIS キーを追加するには、**[Assign a key to your app]\(アプリへのキーの割り当て\)** ダイアログで **[キーの追加]** をクリックします。 

    ![アプリへのキーの割り当て](./media/luis-manage-keys/assign-key.png)
3. ダイアログ ボックスでテナントを選択します。 
 
    > [!Note]
    > Azure では、テナントは、サービスに関連付けられているクライアントまたは組織の Azure Active Directory ID を表します。 以前に個人の Microsoft アカウントで Azure サブスクリプションにサインアップした場合は、既にテナントを持っています。 Azure portal にログインすると、自動的に[お使いの既定のテナント](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant)へのログインが行われます。 このテナントは自由に使用できますが、組織の管理者アカウントを作成する必要がある場合があります。

4. 追加する Azure LUIS キーに関連付けられている Azure サブスクリプションを選択します。

5. Azure LUIS アカウントを選択します。 アカウントのリージョンは、かっこ内に表示されます。 

    ![キーの選択](./media/luis-manage-keys/assign-key-filled-out.png)

6. このエンドポイント キーを割り当てて、すべてのエンドポイントのクエリで使用します。 

<!-- content moved to luis-reference-regions.md, need replacement links-->
<a name="regions-and-keys"></a>
<a name="publishing-to-europe"></a>
<a name="publishing-to-australia"></a>

## <a name="publishing-regions"></a>公開リージョン
公開[リージョン](luis-reference-regions.md) ([ヨーロッパ](luis-reference-regions.md#publishing-to-europe)および[オーストラリア](luis-reference-regions.md#publishing-to-australia)での公開を含む) の詳細を確認してください。 公開リージョンは、オーサリング リージョンとは異なります。 アプリの作成は必ず、必要な公開リージョンに対応するオーサリング リージョンで行ってください。

## <a name="unassign-key"></a>キーの割り当ての解除

* **[Resources and Keys]\(リソースとキー\)** のリストで、割り当てを解除するエンティティの横にあるごみ箱アイコンをクリックします。 次に、確認メッセージで **[OK]** をクリックして、削除を確定します。
 
    ![エンティティの割り当ての解除](./media/luis-manage-keys/unassign-key.png)

> [!NOTE]
> LUIS キーの割り当てを解除しても、キーは Azure サブスクリプションから削除されません。

## <a name="next-steps"></a>次の手順

ご自身のキーを使用して、**[Publish app]\(アプリの公開\)** ページでご自身のアプリを公開します。 公開の手順については、[アプリの公開](luis-how-to-publish-app.md)に関するページをご覧ください。