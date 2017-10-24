---
title: "Postman でカスタム API とカスタム コネクタを記述する- Azure Logic Apps | Microsoft Docs"
description: "カスタム API とカスタム コネクタを記述、グループ化、整理するための Postman Collection を作成します。"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 072d544e5d29c4abb3d69651e53cfb33d5e0c9e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="describe-custom-apis-and-custom-connectors-with-postman"></a>Postman を使用したカスタム API とカスタム コネクタの記述

[カスタム API](../logic-apps/logic-apps-create-api-app.md) と[カスタム コネクタ](../logic-apps/custom-connector-overview.md)を迅速かつ簡単に開発できるようにするには、API とコネクタを記述する際に、OpenAPI ドキュメントではなく [Postman](https://www.getpostman.com/) Collection を作成します。 Postman Collection を使用すると、関連する API 要求を整理し、グループ化できます。 たとえば、Azure Logic Apps、Microsoft Flow、または Microsoft PowerApps のコネクタを作成するときに、Postman をご利用いただけます。 

このチュートリアルでは、例として [Azure Cognitive Services Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/) の [Detect Language API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) を使用して、[Postman Collection](https://www.getpostman.com/docs/postman/collections/creating_collections) を作成する方法を説明します。 この API は、渡されたテキストの言語、センチメント、キー フレーズを識別します。

## <a name="prerequisites"></a>前提条件

* Postman を初めて使用する場合は、[Postman アプリをインストール](https://www.getpostman.com/apps)します。

* Azure サブスクリプション。 サブスクリプションがない場合は、[無料の Azure アカウント](https://azure.microsoft.com/free/)をご利用いただけます。 また、[従量課金のサブスクリプション](https://azure.microsoft.com/pricing/purchase-options/)にサインアップすることもできます。

* Azure サブスクリプションがある場合は、[こちら](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-signup.md)に記載されている手順に従って Text Analytics API にサインアップします。 

## <a name="create-a-postman-collection"></a>Postman Collection の作成

コレクションを作成するには、まず API エンドポイントの HTTP 要求を作成します。 

### <a name="create-an-http-request-for-your-api"></a>API の HTTP 要求を作成する

1. API エンドポイントの [HTTP 要求](https://www.getpostman.com/docs/postman/sending_api_requests/requests)を作成するために Postman アプリを開きます。 詳細については、Postman の[要求に関するドキュメント](https://www.getpostman.com/docs/postman/sending_api_requests/requests)をご覧ください。

   1. **[Builder]\(ビルダー\)** タブで、HTTP メソッドを選択し、API エンドポイントの要求 URL を入力して、承認プロトコルを選択します (存在する場合)。 
   準備ができたら、**[Params]\(パラメーター\)** を選択します。

      このチュートリアルでは、次の例の設定をご利用いただけます。

      ![要求の作成: HTTP メソッド、要求 URL、承認](./media/custom-connector-api-postman-collection/01-create-api-http-request.png)

      | パラメーター | 推奨値 | 
      | --------- | --------------- | 
      | **HTTP メソッド** | POST | 
      | **要求 URL** | `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages` | | 
      | **承認** | "No Auth" | | 
      ||| 

   2. 要求 URL でクエリまたはパス パラメーターとして使用するキーと値のペアを入力します。 Postman では、これらの項目を組み合わせてクエリ文字列を作成します。
   完了したら、**[Headers]\(ヘッダー\)** を選択します。

      ![要求 (続き): パラメーター](./media/custom-connector-api-postman-collection/02-create-api-http-request-params.png)

      | パラメーター | 推奨値 | 
      | --------- | --------------- | 
      | **Params** | **キー**: "numberOfLanguagesToDetect" </br>**値**: "1" | 
      ||| 

   3. 要求ヘッダーのキーと値のペアを入力します。 
   ヘッダー名には任意の文字列を入力します。 一般的な HTTP ヘッダーの場合は、ドロップダウン リストから選択できます。 完了したら、**[Body]\(本文\)** を選択します。 
   
      ![要求 (続き): ヘッダー](./media/custom-connector-api-postman-collection/03-create-api-http-request-header.png)

      | パラメーター | 値 | 
      | --------- | ----- | 
      | **ヘッダー** | **キー**: "Ocp-Apim-Subscription-Key" </br>**値**: *API のサブスクリプション キー*。これは、Cognitive Services アカウントで確認できます。 <p>**キー**: "Content-Type" </br> **値**: "application/json" | 
      ||| 

   4. 要求本文で送信する内容を入力します。 
   応答を取得して要求が機能していることを確認するために、**[Send]\(送信\)** をクリックします。 
   
      ![要求 (続き): 本文](./media/custom-connector-api-postman-collection/04-create-api-http-request-body.png)

      | パラメーター | 推奨値 | 
      | --------- | --------------- |    
      | **本文** | ```{"documents": [{ "id": "1", "text": "Hello World"}]}``` | 
      ||| 

      応答フィールドには、結果やエラー (発生した場合) など、API からの完全な応答が含まれます。

      ![要求の応答の取得](./media/custom-connector-api-postman-collection/05-create-api-http-request-response.png)

2. 要求が機能していることを確認したら、Postman Collection に要求を保存します。 

   1. **[保存]** を選択します。 
      
      ![[Save]\(保存\) を選択](./media/custom-connector-api-postman-collection/06a-save-request.png)
 
   2. **[Save Request]\(要求の保存\)** で、**要求名**を入力し、必要に応じて**要求の説明**を入力します。 

      > [!NOTE]
      > これらの値は、後でカスタム コネクタで使用されます。 そのため、後でカスタム API の操作の概要と説明に使用するものと同じ値を指定する必要があります。

   3. **[+ Create Collection]\(+ コレクションの作成\)** をクリックし、コレクション名を指定します。 
   チェック マークをクリックしてコレクション フォルダーを作成し、**[Save to *your-Postman-collection-name*]\(<Postman Collection 名> に保存\)** をクリックします。

      ![要求の保存](./media/custom-connector-api-postman-collection/06b-save-request.png)

### <a name="save-the-request-response"></a>要求の応答を保存する

要求を保存したら、応答を保存できます。 これにより、後で要求を読み込んだときに、応答がサンプルとして表示されます。

1. 応答ウィンドウの上の **[Save Response]\(応答の保存\)** をクリックします。 

   ![応答の保存](./media/custom-connector-api-postman-collection/07-create-api-http-request-save-response.png)

   カスタム コネクタでは、要求ごとに 1 つの応答のみをサポートします。 
   1 つの要求で複数の応答を保存した場合、最初の応答だけが使用されます。

2. サンプルの名前を指定し、**[Save Example]\(サンプルの保存\)** をクリックします。

3. 要求と応答を追加して、Postman Collection を引き続き作成します。

### <a name="export-your-postman-collection"></a>Postman Collection をエクスポートする

1. コレクションの作成が完了したら、JSON ファイルにコレクションをエクスポートします。

   ![コレクションのエクスポート](./media/custom-connector-api-postman-collection/08-export-http-request.png)

2. エクスポート形式として **[Collection v1]** を選択し、JSON ファイルを保存する場所を参照します。

   > [!NOTE]
   > 現在、カスタム コネクタで機能するのは v1 だけです。

   ![エクスポート形式の選択: "Collection v1"](./media/custom-connector-api-postman-collection/09-export-format.png)
   
これで、この Postman Collection を使用して、カスタム API とカスタム コネクタを作成できるようになりました。 コレクションをエクスポートしたら、Logic Apps、Flow、または PowerApps に JSON ファイルをインポートできます。

> [!IMPORTANT]
> Postman Collection からカスタム コネクタを作成した場合、アクションとトリガーから `Content-type` ヘッダーを必ず削除してください。 このヘッダーは、ターゲット サービス (Flow など) によって自動的に追加されます。 また、`securityDefintions` セクションの認証ヘッダーもアクションとトリガーから削除します。

## <a name="next-steps"></a>次のステップ

* [Logic Apps: カスタム コネクタの登録](../logic-apps/logic-apps-custom-connector-register.md)
* [Flow: コネクタの登録](https://ms.flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector)
* [PowerApps: コネクタの登録](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector)
* [カスタム コネクタに関する FAQ](../logic-apps/custom-connector-faq.md)
