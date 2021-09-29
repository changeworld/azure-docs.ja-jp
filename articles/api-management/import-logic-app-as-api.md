---
title: Azure Portal を使用してロジック アプリを API としてインポートする | Microsoft Docs
description: この記事では、API Management (APIM) を使用してロジック アプリを API としてインポートする方法を示します。
services: api-management
documentationcenter: ''
author: dlepow
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/16/2021
ms.author: danlep
ms.openlocfilehash: 509a143a368c4626b7d0a39e1c3f2d15c8a3d0b2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128667770"
---
# <a name="import-a-logic-app-as-an-api"></a>ロジック アプリを API としてインポート

この記事では、ロジック アプリを API としてインポートし、そのインポート済みの API をテストする方法について説明します。

この記事では、次のことについて説明します。

> [!div class="checklist"]
>
> -   ロジック アプリを API としてインポート
> -   Azure Portal での API のテスト

## <a name="prerequisites"></a>前提条件

-   次のクイック スタートを完了すること:[Azure API Management インスタンスを作成する](get-started-create-service-instance.md)
-   HTTP エンドポイントが公開されるサブスクリプションにロジック アプリがあることを確認します。 詳しくは、[HTTP エンドポイントでのワークフローのトリガー](../logic-apps/logic-apps-http-endpoint.md)に関する記事をご覧ください

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>バックエンド API のインポートと公開

1. Azure portal で API Management サービスに移動し、メニューから **[API]** を選択します。
1. **[Add a new API]\(新しい API の追加\)** の一覧から **[ロジック アプリ]** を選択します。

    :::image type="content" source="./media/import-logic-app-as-api/logic-app-select.png" alt-text="[ロジック アプリ] カテゴリを選択する":::

1. **[参照]** を押して、サブスクリプション内の、HTTP トリガーを持つロジック アプリの一覧を表示します  
    * HTTP トリガーの "*ない*" ロジック アプリは一覧に表示されません。

    :::image type="content" source="./media/import-logic-app-as-api/browse-logic-apps.png" alt-text="適切なトリガーを使用する既存のロジック アプリを参照する":::

1. ロジック アプリを選択します。 

    :::image type="content" source="./media/import-logic-app-as-api/select-logic-app-import-2.png" alt-text="ロジック アプリを選択する":::

1. API Management では、選択したアプリに関連付けられている Swagger が検索されてフェッチされ、インポートされます。
1. API URL サフィックスを追加します。 
    * サフィックスにより、この API Management インスタンスでこの特定の API が一意に識別されます。

    :::image type="content" source="./media/import-logic-app-as-api/create-from-logic-app.png" alt-text="フィールドへの入力を完了する":::

1. API を公開して開発者が利用できるようにする場合は、 **[完全]** ビューに切り替え、 **[製品]** に関連付けます。 この例では、"*無制限*" の製品を使用します。 
    * API は、作成時に製品に追加することも、後で **[設定]** タブを使用して追加することもできます。

    >[!NOTE]
    > 成果物は、開発者ポータルを通じて開発者に提供される 1 つまたは複数の API を関連付けたものです。 開発者は、まず成果物をサブスクライブして API へのアクセス権を取得する必要があります。 サブスクライブすると、その成果物の API に使用されるサブスクリプション キーが提供されます。 API Management インスタンスの作成者は管理者となるため、既定ですべての製品をサブスクライブすることになります。
    >
    > すべての API Management インスタンスは、既定のサンプル成果物を 2 つ備えています。
    > - **スターター**
    > - **無制限**

1. 他の API 設定を入力します。 
    * これらの値は、作成時に設定することも、後で **[設定]** タブに移動して設定することもできます。設定については、[最初の API のインポートと発行](import-and-publish.md#import-and-publish-a-backend-api)に関するチュートリアルをご覧ください。
1. **［作成］** を選択します

## <a name="test-the-api-in-the-azure-portal"></a>Azure Portal での API のテスト

Azure Portal には、API の操作を表示およびテストするための便利な環境が用意されており、操作を直接呼び出すことができます。

:::image type="content" source="./media/import-logic-app-as-api/test-logic-app-api.png" alt-text="ロジック アプリをテストする":::

1. 前の手順で作成した API を選びます。
2. **[テスト]** タブをクリックします。
3. テストする操作を選択します。

    * ページに、クエリ パラメーターとヘッダーのフィールドが表示されます。 
    * この API に関連付けられている成果物のサブスクリプション キーの場合、ヘッダーの 1 つは "Ocp-Apim-Subscription-Key" です。 
    * API Management インスタンスの作成者は、既に管理者になっているので、キーが自動的に入力されます。

4. **[送信]** をクリックします。

    * テストが成功すると、バックエンドから **200 OK** およびデータが応答として返されます。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

>[!NOTE]
>すべてのロジック アプリには、**手動呼び出し** 操作があります。 複数のロジック アプリの API を構成し、競合を回避するには、関数の名前を変更する必要があります。

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
>
> [公開された API の変換と保護](transform-api.md)
