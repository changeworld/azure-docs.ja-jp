---
title: "Azure Portal を使用して API アプリを API としてインポートする | Microsoft Docs"
description: "このチュートリアルでは、API Management (APIM) を使用して API アプリを API としてインポートする方法を示します。"
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
ms.openlocfilehash: d0e1aa6763d96b5a84bbc5fba7dcae690c051eb3
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2017
---
# <a name="import-an-api-app-as-an-api"></a>API アプリを API としてインポートする

この記事では、API アプリを API としてインポートする方法を示します。 APIM API をテストする方法についても説明します。

この記事では、次のことについて説明します:

> [!div class="checklist"]
> * API アプリを API としてインポートする
> * Azure Portal での API のテスト
> * 開発者ポータルでの API のテスト

## <a name="prerequisites"></a>前提条件

+ [Azure API Management インスタンスの作成](get-started-create-service-instance.md)に関するクイックスタートを完了します
+ サブスクリプションに API アプリがあることを確認します。 詳しくは、[App Service のドキュメント](https://docs.microsoft.com/azure/app-service/) をご覧ください

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>バックエンド API のインポートと発行

1. **[API MANAGEMENT]** の下から **[API]** を選択します。
2. **[Add a new API]\(新しい API の追加\)** の一覧から **[API アプリ]** を選択します。

    !(API アプリ)[./media/import-api-app-as-api/api-app.png]
3. **[参照]** をクリックしてサブスクリプション内の API Apps の一覧を表示します。
4. アプリを選びます。 APIM では、選んだアプリに関連付けられている Swagger が検索されてフェッチされ、インポートされます。 

    Swagger が見つからない場合、APIM は API を "パススルー" API として公開します。 
5. API URL サフィックスを追加します。 サフィックスは、この APIM インスタンスでこの特定の API を識別する名前です。 この APIM インスタンス内で一意である必要があります。
6. API を成果物に関連付けることで API を発行します。 この場合は、"*無制限*" の成果物が使用されます。  API を発行して開発者が利用できるようにするには、その API を成果物に追加します。 API の作成時に行うことも、後で設定することもできます。

    成果物は、1 つまたは複数の API の関連付けです。 複数の API を含めて、開発者ポータルを通じてそれらを開発者に提供できます。 開発者は、まず成果物をサブスクライブして API へのアクセス権を取得する必要があります。 サブスクライブすると、その成果物の API に適したサブスクリプション キーを受け取ります。 APIM インスタンスを作成した場合は、既に管理者になっているので、既定ですべての成果物をサブスクライブしています。

    すべての API Management インスタンスは、2 つのサンプル成果物を既定で備えています。

    * **スターター**
    * **無制限**   
7. **[作成]**を選択します。

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>Azure Portal での新しい APIM API のテスト

Azure Portal には、API の操作を見てテストするための便利な環境が用意されており、操作を直接呼び出すことができます。  

1. 前の手順で作成した API を選びます。
2. **[テスト]** タブをクリックします。
3. いくつかの操作を選びます。

    ページに、クエリ パラメーターのフィールドとヘッダーのフィールドが表示されます。 この API に関連付けられている成果物のサブスクリプション キーの場合、ヘッダーの 1 つは "Ocp-Apim-Subscription-Key" です。 APIM インスタンスを作成した場合は、既に管理者になっているので、キーは自動的に入力されます。 
1. **[送信]** をクリックします。

    バックエンドは "**200 OK**" といくつかのデータで応答します。

## <a name="call-operation"></a>開発者ポータルから操作を呼び出す

操作を**開発者ポータル**から呼び出して API をテストすることもできます。 

1. "バックエンド API のインポートと発行" の手順で作成した API を選びます。
2. **[開発者ポータル]** をクリックします。

    "開発者ポータル" サイトが開きます。
3. 作成した **API** を選びます。
4. テストする操作をクリックします。
5. **[テスト]** をクリックします。
6. **[送信]** をクリックします。
    
    操作を呼び出すと、**応答のステータス**、**応答ヘッダー**、**応答内容**が開発者ポータルに表示されます。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [発行された API の変換と保護](transform-api.md)