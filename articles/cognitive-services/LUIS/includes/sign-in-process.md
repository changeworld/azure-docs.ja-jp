---
title: インクルード ファイル
description: インクルード ファイル
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 04/23/2021
ms.topic: include
ms.openlocfilehash: 727178c2851e43c787c2b1fcf1e7bb349b3ea4f5
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/25/2021
ms.locfileid: "107948362"
---
## <a name="sign-in-to-luis-portal"></a>LUIS ポータルにサインインする

[!INCLUDE [Note about portal deprecation](luis-portal-note.md)]

LUIS の新しいユーザーは、次の手順に従う必要があります。

1. [LUIS ポータル](https://www.luis.ai)にサインインし、国/地域を選択して利用規約に同意します。 代わりに **[マイ アプリ]** が表示される場合は、LUIS リソースが既に存在しているため、次の手順に進んでアプリを作成してください。 そうでない場合は、Azure リソースの使用によって開始します。そのようにすると、LUIS アカウントを新規または既存の Azure 作成リソースにリンクすることができます。 <!---This is equivalent to signing up already migrated. You won't need to go through the [migration process](../luis-migration-authoring.md#what-is-migration) later on.-->
<!---
    * Using a trial key. This allows you to sign in to LUIS with a trial resource that you don't need to set up. If you choose this option, you will eventually be required to [migrate your account](../luis-migration-authoring.md#migration-steps) and link your applications to an authoring resource.
-->

2. 表示された **[Choose an authoring]\(作成の選択\)** ウィンドウで、Azure サブスクリプションと LUIS 作成リソースを見つけます。 リソースがない場合は、新しいものを作成できます。

    <!---:::image type="content" source="../media/luis-how-to-azure-subscription/choose-authoring-resource.png" alt-text="Choose a type of Language Understanding authoring resource.":::
    -->
    ![Language Understanding の作成リソースの種類を選択します。](../media/luis-how-to-azure-subscription/choose-authoring-resource.png)
    
    新しい作成リソースを作成するときは、次の情報を指定します。
    * **[テナント名]** - Azure サブスクリプションが関連付けられているテナント。
    * **[Azure サブスクリプション名]** - リソースに対して課金されるサブスクリプション。
    * **[Azure リソース グループ名]** - 選択または作成するカスタム リソース グループ名。 リソース グループを使用すると、アクセスと管理のために Azure リソースをグループ化できます。
    * **[Azure リソース名]** - 選択するカスタム名。作成および予測エンドポイント クエリの URL の一部として使用されます。
    * **[Pricing tier]\(価格レベル\)** - 価格レベルによって、1 秒および 1 か月あたりの最大トランザクション数が決まります。


