---
title: Azure Portal を使用して手動で API を追加する | Microsoft Docs
description: このチュートリアルでは、API Management (APIM) を使用して手動で API を追加する方法を示します。
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: tutorial
ms.date: 04/26/2021
ms.author: danlep
ms.custom: fasttrack-edit
ms.openlocfilehash: 07b200c67b831b7d7fb01ea8da657e9bcc9b3b18
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "129997007"
---
# <a name="add-an-api-manually"></a>手動による API の追加

この記事の手順では、Azure Portal を使用して API Management (APIM) インスタンスに手動で API を追加する方法を示します。 空の API を作成して手動で定義する必要のある一般的なシナリオは、モック API を作成する場合です。 モック API の作成について詳しくは、[API の応答の模擬テストの実行](mock-api-responses.md)に関するページをご覧ください。

既存の API をインポートする場合は、「[関連トピック](#related-topics)」セクションをご覧ください。

この記事では、空の API を作成し、バックエンド API として [httpbin.org](https://httpbin.org) (パブリック テスト サービス) を指定します。

## <a name="prerequisites"></a>前提条件

次のクイック スタートを完了すること:[Azure API Management インスタンスを作成する](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-an-api"></a>API の作成

1. Azure portal で API Management サービスに移動し、メニューから **[API]** を選択します。
2. 左側のメニューで、 **[+ API の追加]** を選びます。
3. 一覧から **[空の API]** を選びます。  
    ![空の API](media/add-api-manually/blank-api.png)  
4. API の設定を入力します。 設定については、「[最初の API のインポートと発行](import-and-publish.md#import-and-publish-a-backend-api)」のチュートリアルで説明されています。
5. **［作成］** を選択します

この時点では、バックエンド API の操作にマッピングされる API Management の操作はありません。 API Management ではなくバックエンドを介して公開される操作を呼び出すと、**404** が返されます。

>[!NOTE] 
> 既定では、追加する API がバックエンド サービスに接続されていても、許可されるまでは APIM ではいかなる操作も公開されません。 バックエンド サービスの操作を許可するには、バックエンドの操作にマッピングされる APIM の操作を作成します。

## <a name="add-and-test-an-operation"></a>操作の追加とテスト

このセクションでは、バックエンドの "http://httpbin.org/get" 操作にマッピングするために "/get" 操作を追加する方法を示します。

### <a name="add-an-operation"></a>操作の追加

1. 前の手順で作成した API を選びます。
2. **[+ 操作の追加]** を選択します。
3. **[URL]** で、**GET** を選択し、リソースに「`/get`」と入力します。
4. **[表示名]** に「*FetchData*」と入力します。
5. **[保存]** を選択します。

### <a name="test-an-operation"></a>操作のテスト

Azure Portal で操作をテストします。 または、**開発者ポータル** 内でテストすることもできます。

1. **[テスト]** タブを選びます。
2. **FetchData** を選びます。
3. **[送信]** をクリックします。

"http://httpbin.org/get" 操作によって生成される応答が表示されます。 操作を変換する場合は、「[API を変換および保護する](transform-api.md)」をご覧ください。

## <a name="add-and-test-a-parameterized-operation"></a>パラメーター化された操作の追加とテスト

このセクションでは、パラメーターを受け取る操作を追加する方法を示します。 この場合、操作を "http://httpbin.org/status/200" にマッピングします。

### <a name="add-the-operation"></a>操作の追加

1. 前の手順で作成した API を選びます。
2. **[+ 操作の追加]** を選択します。
3. **[URL]** で、**GET** を選択し、リソースに「`*/status/{code}`」と入力します。 必要に応じて、このパラメーターに関連付けられている情報を指定できます。 たとえば、 **[種類]** に「*Number*」を、 **[値]** に「*200*」(既定値) を入力します。
4. **[表示名]** に「WildcardGet」と入力します。
5. **[保存]** を選択します。

### <a name="test-the-operation"></a>操作のテスト 

Azure Portal で操作をテストします。  または、**開発者ポータル** 内でテストすることもできます。

1. **[テスト]** タブを選びます。
2. **WildcardGet** を選択します。 既定では、コード値は "*200*" に設定されます。 値を変更して他の値をテストできます。 たとえば、「*418*」と入力します。
3. **[送信]** をクリックします。

    "http://httpbin.org/status/200" 操作によって生成される応答が表示されます。 操作を変換する場合は、「[API を変換および保護する](transform-api.md)」をご覧ください。

## <a name="add-and-test-a-wildcard-operation"></a>ワイルドカード操作の追加とテスト

このセクションでは、ワイルドカード操作を追加する方法を説明します。 ワイルドカード操作を使用すると、API 要求で任意の値を渡すことができます。 前のセクションで示したように個別の GET 操作を作成するのではなく、ワイルドカード GET 操作を作成できます。

### <a name="add-the-operation"></a>操作の追加

1. 前の手順で作成した API を選びます。
2. **[+ 操作の追加]** を選択します。
3. **[URL]** で、**GET** を選択し、リソースに「`/*`」と入力します。
4. **[表示名]** に「*WildcardGet*」と入力します。
5. **[保存]** を選択します。

### <a name="test-the-operation"></a>操作のテスト 

Azure Portal で操作をテストします。  または、**開発者ポータル** 内でテストすることもできます。

1. **[テスト]** タブを選びます。
2. **WildcardGet** を選択します。 前のセクションでテストした 1 つ以上の GET 操作を試すか、サポートされている別の GET 操作を試します。 

    たとえば、 **[テンプレート パラメーター]** で、ワイルドカード (*) 名の横にある値を `headers` に更新します。 この操作により、受信要求の HTTP ヘッダーが返されます。
1. **[送信]** をクリックします。

    "http://httpbin.org/headers" 操作によって生成される応答が表示されます。 操作を変換する場合は、「[API を変換および保護する](transform-api.md)」をご覧ください。
  
>[!NOTE] 
> 統合するバックエンド API のホストが操作 URL の末尾のスラッシュを処理する方法を理解することが重要な場合があります。 詳細については、こちらの [API Management の FAQ](./api-management-faq.yml#how-does-api-management-handle-trailing-slashes-when-calling-backend-services-) に関するページを参照してください。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [公開された API の変換と保護](transform-api.md)
