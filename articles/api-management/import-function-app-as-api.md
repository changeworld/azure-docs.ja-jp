---
title: "Azure Portal を使用して Function App を API としてインポートする | Microsoft Docs"
description: "このチュートリアルでは、API Management (APIM) を使用して Function App を API としてインポートする方法について説明します。"
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/22/2017
ms.author: apimpm
ms.openlocfilehash: c6c7b1f3c2cba9d9f99f7ee1a8e0518bc30f0d27
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2017
---
# <a name="import-a-function-app-as-an-api"></a>API としての Function App のインポート

この記事では、Function App を API としてインポートする方法について説明します。 また、APIM API をテストする方法についても説明します。

この記事では、次のことについて説明します:

> [!div class="checklist"]
> * API としての Function App のインポート
> * Azure Portal での API のテスト
> * 開発者ポータルでの API のテスト

## <a name="prerequisites"></a>前提条件

+ [Azure API Management インスタンスの作成](get-started-create-service-instance.md)に関するクイック スタートを完了します。
+ サブスクリプションに Function App があることを確認します。 詳細については、「[Function App を作成する](../azure-functions/functions-create-first-azure-function.md#create-a-function-app)」をご覧ください。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>バックエンド API のインポートと公開

1. **[API MANAGEMENT]** で **[API]** を選択します。
2. **[Add a new API]\(新しい API の追加\)** の一覧から **[Function App]** を選択します。

    ![関数アプリ](./media/import-function-app-as-api/function-app-api.png)
3. **[参照]** をクリックして、サブスクリプション内の Function App の一覧を表示します。
4. アプリを選択します。 APIM では、選択したアプリに関連付けられている Swagger が検索されてフェッチされ、インポートされます。 
5. API URL サフィックスを追加します。 サフィックスは、この APIM インスタンスでこの特定の API を識別する名前です。 この APIM インスタンス内で一意である必要があります。
6. API を成果物に関連付けることによって、API を公開します。 この場合、"*無制限*" の成果物が使用されます。  API を公開して開発者が利用できるようにする場合は、その API を成果物に追加します。 これは、API の作成時に実行することも、後で設定することもできます。

    成果物は、1 つまたは複数の API の関連付けです。 複数の API を含め、開発者ポータルを通じてそれらを開発者に提供できます。 開発者は、まず成果物をサブスクライブして API へのアクセス権を取得する必要があります。 サブスクライブすると、その成果物の API に適したサブスクリプション キーを受け取ります。 APIM インスタンスを作成した場合は、既に管理者になっているので、既定ですべての成果物をサブスクライブしています。

    すべての API Management インスタンスは、2 つのサンプル成果物を既定で備えています。

    * **スターター**
    * **無制限**   
7. **[作成]**を選択します。

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>Azure Portal での新しい APIM API のテスト

Azure Portal には、API の操作を表示およびテストするための便利な環境が用意されており、操作を直接呼び出すことができます。  

1. 前の手順で作成した API を選択します。
2. **[テスト]** タブをクリックします。
3. 操作を選択します。

    ページに、クエリ パラメーターのフィールドとヘッダーのフィールドが表示されます。 この API に関連付けられている成果物のサブスクリプション キーの場合、ヘッダーの 1 つは "Ocp-Apim-Subscription-Key" です。 APIM インスタンスを作成した場合は、既に管理者になっているので、キーが自動的に入力されます。 
1. **[送信]** をクリックします。

    バックエンドは "**200 OK**" といくつかのデータで応答します。

## <a name="call-operation"></a>開発者ポータルから操作を呼び出す

操作を**開発者ポータル**から呼び出して API をテストすることもできます。 

1. 「バックエンド API のインポートと公開」で作成した API を選択します。
2. **[開発者ポータル]** をクリックします。

    "開発者ポータル" サイトが開きます。
3. 作成した **API** を選択します。
4. テストする操作をクリックします。
5. **[テスト]** をクリックします。
6. **[送信]** をクリックします。
    
    操作を呼び出すと、**応答のステータス**、**応答ヘッダー**、**応答内容**が開発者ポータルに表示されます。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [公開された API の変換と保護](transform-api.md)