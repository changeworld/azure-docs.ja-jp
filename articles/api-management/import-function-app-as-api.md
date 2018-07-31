---
title: Azure portal を使用して Azure Functions アプリを API としてインポートする | Microsoft Docs
description: このチュートリアルでは、Azure API Management を使用して Azure Functions アプリを API としてインポートする方法について説明します。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/15/2018
ms.author: apimpm
ms.openlocfilehash: 670fa58de7155028b0f72f1f819b9f269e07b9eb
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2018
ms.locfileid: "39239054"
---
# <a name="import-an-azure-functions-app-as-an-api"></a>Azure Functions アプリを API としてインポートする

この記事では、Azure Functions アプリを API としてインポートする方法について説明します。 また、Azure API Management API をテストする方法についても説明します。

この記事では、次のことについて説明します:

> [!div class="checklist"]
> * API としての Functions アプリのインポート
> * Azure Portal での API のテスト
> * 開発者ポータルでの API のテスト

## <a name="prerequisites"></a>前提条件

+ [Azure API Management インスタンスの作成](get-started-create-service-instance.md)に関するクイック スタートを完了します。
+ サブスクリプションに Azure Functions アプリがあることを確認します。 詳しくは、[Functions アプリの作成](../azure-functions/functions-create-first-azure-function.md#create-a-function-app)に関するセクションをご覧ください。
+ Azure Functions アプリの [OpenAPI 定義を作成](../azure-functions/functions-openapi-definition.md)します。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"></a>バックエンド API のインポートと公開

1. **[API MANAGEMENT]** で **[API]** を選択します。
2. **[Add a new API]\(新しい API の追加\)** の一覧から **[Functions App]\(Functions アプリ\)** を選択します。

    ![Functions アプリ](./media/import-function-app-as-api/function-app-api.png)
3. **[参照]** をクリックして、サブスクリプション内の Functions アプリの一覧を表示します。
4. アプリを選びます。 API Management では、選択したアプリに関連付けられている Swagger が検索されてフェッチされ、インポートされます。 
5. API URL サフィックスを追加します。 サフィックスは、この API Management インスタンスでこの特定の API を識別する名前です。 サフィックスは、この API Management インスタンス内で一意である必要があります。
6. API を成果物に関連付けることで API を公開します。 この場合、**無制限**の成果物が使用されます。 API を公開して開発者が利用できるようにするには、その API を成果物に追加します。 API は API を作成するとき、または後で成果物に追加できます。

    製品には、1 つまたは複数の API が関連付けられています。 複数の API を組み込み、開発者ポータルを通じてそれらを開発者に提供できます。 開発者は、まず成果物をサブスクライブして API へのアクセス権を取得する必要があります。 開発者がサブスクライブすると、その成果物の API に適したサブスクリプション キーを受け取ります。 API Management インスタンスを作成したユーザーが管理者になります。 管理者は、既定ですべての成果物をサブスクライブしています。

    すべての API Management インスタンスは、2 つのサンプル成果物を既定で備えています。

    * **スターター**
    * **無制限**   
7. **[作成]** を選択します。

## <a name="populate-azure-functions-keys-in-azure-api-management"></a>Azure API Management での Azure 関数キーの入力

インポートされた Azure Functions アプリがキーによって保護される場合、それらの*名前付きの値*が API Management によって自動的に作成されます。 API Management ではエントリへのシークレットの入力は行われません。 各エントリについて次の手順を実行します。  

1. API Management インスタンスで、**[名前付きの値]** タブを選択します。
2. エントリを選択して、サイド バーの **[値の表示]** を選択します。

    ![名前付きの値](./media/import-function-app-as-api/apim-named-values.png)

3. **[値]** ボックスに表示されているテキストが **code for \<Azure Functions の名前\>** に似ている場合は、**[Functions アプリ]** に移動し、**[Functions]** に移動します。
4. **[管理]** を選択し、アプリの認証方法に基づいて関連するキーをコピーします。

    ![Functions アプリ - キーのコピー](./media/import-function-app-as-api/azure-functions-app-keys.png)

5. キーを **[値]** ボックスに貼り付け、**[保存]** を選択します。

    ![Functions アプリ - キー値の貼り付け](./media/import-function-app-as-api/apim-named-values-2.png)

## <a name="test-the-new-api-management-api-in-the-azure-portal"></a>Azure portal での新しい API Management API のテスト

操作は Azure portal から直接呼び出すことができます。 Azure portal は API の操作を表示およびテストするのに便利です。  

1. 前のセクションで作成した API を選択します。
2. **[テスト]** タブを選びます。
3. 操作を選択します。

    ページに、クエリ パラメーターのフィールドとヘッダーのフィールドが表示されます。 この API に関連付けられている成果物のサブスクリプション キーの場合、ヘッダーの 1 つは **Ocp-Apim-Subscription-Key** です。 API Management インスタンスを作成した場合は、既に管理者になっているので、キーが自動的に入力されます。 
4. **[送信]** を選択します。

    バックエンドは **200 OK** といくつかのデータで応答します。

## <a name="call-operation"></a>開発者ポータルから操作を呼び出す

操作を開発者ポータルから呼び出して API をテストすることもできます。 

1. 「[バックエンド API のインポートと公開](#create-api)」で作成した API を選択します。
2. **[開発者ポータル]** を選択します。

    開発者ポータルのサイトが開きます。
3. 作成した **API** を選びます。
4. テストする操作を選択します。
5. **[Try it]\(試してみる\)** を選択します。
6. **[送信]** を選択します。
    
    操作を呼び出すと、**応答のステータス**、**応答ヘッダー**、**応答内容**が開発者ポータルに表示されます。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [公開された API の変換と保護](transform-api.md)