---
title: インクルード ファイル
description: インクルード ファイル
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: 1effd07cef439a6257028549b7b7114b742eb478
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488764"
---
## <a name="sign-in-to-luis-portal"></a>LUIS ポータルにサインインする

LUIS の新しいユーザーは、次の手順に従う必要があります。

1. [LUIS ポータル](https://www.luis.ai)にサインインし、国を選択して利用規約に同意します。
1. **[Create Azure resource]\(Azure リソースの作成\)** を選択し、 **[Create an authoring resource to migrate your apps to]\(アプリの移行先となるオーサリング リソースの作成\)** を選択します。

    ![Language Understanding のオーサリング リソースのタイプを選択する](../media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. リソースの詳細を入力します。

    ![オーサリング リソースを作成する](../media/migrate-authoring-key/choose-authoring-resource-form.png)

    **新しい作成リソースを作成する**ときは、次の情報を指定します。 

    * **[Resource name]\(リソース名\)** - 選択したカスタム名。オーサリングおよび予測エンドポイント クエリの URL の一部として使用されます。
    * **[Tenant]\(テナント\)** - Azure サブスクリプションが関連付けられているテナント。 
    * **[Subscription name]\(サブスクリプション名\)** - リソースに対して課金されるサブスクリプション。
    * **[Resource group]\(リソース グループ\)** - 選択または作成するカスタム リソース グループ名。 リソース グループを使用すると、アクセスと管理のために Azure リソースをグループ化できます。 
    * **[Location]\(場所\)** - 場所の選択肢は、**リソース グループ**の選択に基づいて決まります。
    * **[Pricing tier]\(価格レベル\)** - 価格レベルによって、1 秒および 1 か月あたりの最大トランザクション数が決まります。

1. 作成するリソースの概要が表示されます。 **[次へ]** を選択します。

    ![オーサリング リソースを作成する](../media/sign-in/sign-in-confirm-key-selection.png)

1. **[続行]** を選択して確認します。 

    ![オーサリング リソースを作成する](../media/sign-in/sign-in-confirm-continue.png)