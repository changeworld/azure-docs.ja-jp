---
title: Azure Portal を使用して Function App を API としてインポートする | Microsoft Docs
description: このチュートリアルでは、API Management (APIM) を使用して Function App を API としてインポートする方法について説明します。
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
ms.openlocfilehash: 0ee83446bb08e66c7f325bdd5585b8cc0484a74e
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090930"
---
# <a name="import-a-function-app-as-an-api"></a>API としての Function App のインポート

この記事では、Function App を API としてインポートする方法について説明します。 また、APIM API をテストする方法についても説明します。

この記事では、次のことについて説明します:

> [!div class="checklist"]
> * API としての Function App のインポート
> * Azure Portal での API のテスト
> * 開発者ポータルでの API のテスト

## <a name="prerequisites"></a>前提条件

+ [Azure API Management インスタンスの作成](get-started-create-service-instance.md)に関するクイックスタートを完了します
+ サブスクリプションに Azure 関数アプリがあることを確認します。 詳細については、「[Function App を作成する](../azure-functions/functions-create-first-azure-function.md#create-a-function-app)」をご覧ください。
+ Azure 関数アプリの [OpenAPI 定義を作成します](../azure-functions/functions-openapi-definition.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>バックエンド API のインポートと公開

1. **[API Management]** で **[API]** を選びます。
2. **[Add a new API]\(新しい API の追加\)** の一覧から **[Function App]** を選択します。

    ![関数アプリ](./media/import-function-app-as-api/function-app-api.png)
3. **[参照]** をクリックして、サブスクリプション内の Function App の一覧を表示します。
4. アプリを選択します。 APIM では、選択したアプリに関連付けられている Swagger が検索されてフェッチされ、インポートされます。 
5. API URL サフィックスを追加します。 サフィックスは、この APIM インスタンスでこの特定の API を識別する名前です。 この APIM インスタンス内で一意である必要があります。
6. API を成果物に関連付けることで API を公開します。 この場合、"*無制限*" の成果物が使用されます。  API を公開して開発者が利用できるようにするには、その API を成果物に追加します。 API の作成時に行うことも、後で設定することもできます。

    製品には、1 つまたは複数の API が関連付けられています。 複数の API を組み込み、開発者ポータルを通じてそれらを開発者に提供できます。 開発者は、まず成果物をサブスクライブして API へのアクセス権を取得する必要があります。 サブスクライブすると、その成果物の API に適したサブスクリプション キーを受け取ります。 APIM インスタンスを作成した場合は、既に管理者になっているため、既定ですべての製品をサブスクライブしています。

    すべての API Management インスタンスは、2 つのサンプル成果物を既定で備えています。

    * **スターター**
    * **無制限**   
7. **[作成]** を選択します。

## <a name="populate-azure-functions-keys-in-azure-api-management"></a>Azure API Management での Azure 関数キーの入力

インポートされた Azure 関数がキーによって保護される場合、それらの**名前付きの値**が Azure API Management によって自動的に作成されます。ただし、エントリへのシークレットの入力は行われません。 各エントリについて、以下の手順を実行する必要があります。  

1. API Management インスタンスの **[名前付きの値]** タブに移動します。
2. エントリをクリックして、サイド バーの **[値の表示]** をクリックします。

    ![名前付きの値](./media/import-function-app-as-api/apim-named-values.png)

3. 内容が "*<Azure 関数名> のコード*" のような場合、インポートされた Azure 関数アプリに進んで、Azure 関数に移動します。
4. Azure 関数の認証方法に基づいて、目的の Azure 関数の **[管理]** セクションに移動して関連するキーをコピーします。

    ![関数アプリ](./media/import-function-app-as-api/azure-functions-app-keys.png)

5. **[名前付きの値]** でテキスト ボックスにキーを貼り付けて、**[保存]** をクリックします。

    ![関数アプリ](./media/import-function-app-as-api/apim-named-values-2.png)

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>Azure Portal での新しい APIM API のテスト

Azure Portal には、API の操作を表示およびテストするための便利な環境が用意されており、操作を直接呼び出すことができます。  

1. 前の手順で作成した API を選びます。
2. **[テスト]** タブをクリックします。
3. いくつかの操作を選びます。

    ページに、クエリ パラメーターのフィールドとヘッダーのフィールドが表示されます。 この API に関連付けられている成果物のサブスクリプション キーの場合、ヘッダーの 1 つは "Ocp-Apim-Subscription-Key" です。 APIM インスタンスを作成した場合は、既に管理者になっているので、キーが自動的に入力されます。 
1. **[送信]** をクリックします。

    バックエンドは **200 OK** といくつかのデータで応答します。

## <a name="call-operation"></a>開発者ポータルから操作を呼び出す

操作を**開発者ポータル**から呼び出して API をテストすることもできます。 

1. "バックエンド API のインポートと公開" の手順で作成した API を選びます。
2. **[開発者ポータル]** をクリックします。

    "開発者ポータル" サイトが開きます。
3. 作成した **API** を選びます。
4. テストする操作をクリックします。
5. **[テスト]** をクリックします。
6. **[送信]** をクリックします。
    
    操作を呼び出すと、**応答のステータス**、**応答ヘッダー**、**応答内容**が開発者ポータルに表示されます。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [公開された API の変換と保護](transform-api.md)