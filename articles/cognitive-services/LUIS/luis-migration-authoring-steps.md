---
title: Azure オーサリング リソースに移行する
titleSuffix: Azure Cognitive Services
description: Azure オーサリング リソースに移行します。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: diberry
ms.openlocfilehash: 13eb301daadb70456c0c0d7db8991d7a695b1de9
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259631"
---
# <a name="steps-to-migrate-to-the-azure-authoring-resource"></a>Azure オーサリング リソースに移行するための手順

Azure オーサリング リソースを使用するには、Language Understanding (LUIS) ポータルから、所有しているすべてのアプリを移行します。

## <a name="prerequisites"></a>前提条件

* **必要に応じて**、各アプリをエクスポートするかエクスポート [API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) を使用して、LUIS ポータルのアプリの一覧からアプリをバックアップします。
* **必要に応じて**、各アプリのコラボレーターの一覧を保存します。 このメールの一覧は、移行プロセスの一部として提供されています。
* **必須**。[Azure サブスクリプション](https://azure.microsoft.com/free/)が必要です。 サブスクリプション プロセスの一部では、課金情報が必要です。 ただし、LUIS を使用する際には、無料 (F0) 価格レベルを使用できます。 使用量が増えるにつれて、最終的には有料レベルが必要になることがあります。 

Azure サブスクリプションをお持ちでない場合は、[サインアップ](https://azure.microsoft.com/free/)してください。 

## <a name="access-the-migration-process"></a>移行プロセスにアクセスする

毎週、アプリを移行するかどうかを確認するメッセージが表示されます。 移行せずにこのウィンドウをキャンセルすることもできます。 次にスケジュールされている期間より前に移行する場合は、LUIS ポータル上部のツール バーにある**ロック** アイコンから移行プロセスを開始できます。 

## <a name="app-owner-begins-the-migration-process"></a>アプリ所有者が移行プロセスを開始する

移行プロセスは、LUIS アプリの所有者である場合に実行できます。 

1. 1. [LUIS ポータル](https://www.luis.ai) にサインインし、使用条件に同意します。
1. 移行のポップアップ ウィンドウでは、移行を続行するか、後で移行するかを選択できます。 **[Migrate now]\(今すぐ移行\)** を選択します。 後で移行することを選択した場合は、9 か月以内に Azure の新しいオーサリング キーに移行してください。

    ![移行プロセスの最初のポップアップ ウィンドウで、[Migrate now]\(今すぐ移行\) を選択します。](./media/migrate-authoring-key/migrate-now.png)

1. いずれかのアプリにコラボレーターがいる場合は、移行について通知する**メールを送信する**よう求められます。 これは省略可能な手順です。 既定のメール アプリケーションが開き、書式が設定されていないメールが表示されます。 

    ![移行について通知するメールをコラボレーターに送信する](./media/migrate-authoring-key/send-collaborators-email.png)

1. **[Start by creating an authoring resource to migrate your apps to]\(アプリの移行先となるオーサリング リソースの作成から開始します\)** を選択して、LUIS オーサリング リソースを作成することを選択します。 

    ![オーサリング リソースを作成する](./media/migrate-authoring-key/choose-authoring-resource.png)

1. 次のウィンドウで、リソース キーの情報を入力します。 情報を入力したら、 **[Create resource]\(リソースの作成\)** を選択します。 

    ![オーサリング リソースを作成する](./media/migrate-authoring-key/choose-authoring-resource-form.png)

    **新しい作成リソースを作成する**ときは、次の情報を指定します。 

    * **[Resource name]\(リソース名\)** - 選択したカスタム名。オーサリングおよび予測エンドポイント クエリの URL の一部として使用されます。
    * **[Tenant]\(テナント\)** - Azure サブスクリプションが関連付けられているテナント。 
    * **[Subscription name]\(サブスクリプション名\)** - リソースに対して課金されるサブスクリプション。
    * **[Resource group]\(リソース グループ\)** - 選択または作成するカスタム リソース グループ名。 リソース グループを使用すると、アクセスと管理のために Azure リソースをグループ化できます。 
    * **[Location]\(場所\)** - 場所の選択肢は、**リソース グループ**の選択に基づいて決まります。
    * **[Pricing tier]\(価格レベル\)** - 価格レベルによって、1 秒および 1 か月あたりの最大トランザクション数が決まります。 

1. オーサリング リソースが作成されると、成功メッセージが表示されます。 **[Close]\(閉じる\)** を選択してポップアップ ウィンドウを閉じます。

    ![オーサリング リソースが正常に作成されました。](./media/migrate-authoring-key/migration-success.png)

    **[My apps]\(マイ アプリ\)** の一覧に、新しいオーサリング リソースに移行されたアプリが表示されます。 

    LUIS ポータルでアプリの編集を続けるために、オーサリング リソースのキーの情報は必要ありません。 プログラムを使用してアプリを編集する場合は、オーサリング キーの値が必要です。 これらの値は、LUIS ポータルの **[Manage]\(管理\) -> [Azure resources]\(Azure リソース\)** ページのほか、Azure portal のそのリソースの **[キー]** ページでも確認できます。  

## <a name="after-the-migration-process-add-contributors-to-your-authoring-resource"></a>移行プロセスが完了した後、オーサリング リソースに共同作成者を追加する

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

[共同作成者を追加する方法](luis-how-to-collaborate.md)を確認してください。 

## <a name="next-steps"></a>次の手順


* オーサリング キーとランタイム キーの[概念](luis-concept-keys.md)について見直す
* [キーを割り当てる方法](luis-how-to-azure-subscription.md)と[共同作成者](luis-how-to-collaborate.md)を追加する方法について見直す
