---
title: Azure Web アプリを Azure API Management にインポートする | Microsoft Docs
description: この記事では、Azure API Management を使用して、Azure App Service でホストされている Web API をインポートする方法について説明します。
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 04/27/2021
ms.author: danlep
ms.openlocfilehash: 8d98a6c115b7f3d06b80a938c090bfa1b9c9794e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128585092"
---
# <a name="import-an-azure-web-app-as-an-api"></a>Azure Web アプリを API としてインポートする

この記事では、Azure portal を使用して、Azure Web アプリを Azure API Management にインポートし、インポートした API をテストする方法について説明します。

> [!NOTE]
> Visual Studio Code 用の API Management 拡張機能を使用して API をインポートおよび管理できます。 [API Management 拡張機能のチュートリアル](visual-studio-code-tutorial.md)に従ってインストールしてから作業を開始してください。

この記事では、次の方法について説明します。

> [!div class="checklist"]
> * App Service でホストされる Web アプリのインポート
> * Azure Portal での API のテスト

## <a name="expose-web-app-with-api-management"></a>API Management を使用した Web アプリの公開

[Azure App Service](../app-service/overview.md) は、Web アプリケーション、REST API、およびモバイル バックエンドをホストするための HTTP ベースのサービスです。 API 開発者は、任意のテクノロジ スタックとパイプラインを使用して、API を開発し、セキュリティで保護されたスケーラブルな環境に Web アプリとして API バックエンドを公開することができます。 その後、API Management を使用して、Web アプリを公開し、ライフサイクル全体を通じて API を管理および保護し、利用者に発行します。

Web アプリでホストされる API を公開するための環境としては、次のような理由から API Management が推奨されます。

* API 利用者に公開されるフロントエンドの管理とセキュリティによる保護を、バックエンド Web アプリの管理と監視から分離します
* Web アプリとしてホストされる Web API を、他の API と同じ環境で管理します
* [ポリシー](api-management-policies.md)を適用して、呼び出しレート制限などの API の動作を変更します
* API 利用者は API Management のカスタマイズ可能な[開発者ポータル](api-management-howto-developer-portal.md)に誘導され、そこで API を検索し、情報を入手し、アクセスを申請し、試用することができます

詳細については、「[API Management について](api-management-key-concepts.md)」を参照してください。

## <a name="openapi-specification-versus-wildcard-operations"></a>OpenAPI 仕様とワイルドカード操作

API Management では、App Service でホストされ、OpenAPI 仕様 (Swagger 定義) を含んでいる Web アプリのインポートをサポートしています。 ただし、OpenAPI 仕様は必須ではありません。

* Web アプリで OpenAPI 仕様が API 定義に構成されている場合、必要なパス、パラメーター、応答の種類など、定義に直接マップする API 操作が API Management によって作成されます。 

  OpenAPI 仕様を含めると、API は高い忠実性で API Management にインポートされ、各操作の構成を個別に検証、管理、セキュリティ保護、更新できる柔軟性が得られるため、含めることが推奨されています。

* OpenAPI 仕様が提供されない場合、一般的な HTTP 動詞 (GET、PUT など) の[ワイルドカード操作](add-api-manually.md#add-and-test-a-wildcard-operation)が API Management によって生成されます。 必要なパスやパラメーターをワイルドカード操作に付加して、API 要求をバックエンド API に渡します。

  ワイルドカード操作によって、同じ API Management 機能を引き続き利用できますが、操作は既定では同じ詳細レベルで定義されていません。 いずれの場合も、インポートした API の操作の[編集](edit-api.md)または[追加](add-api-manually.md)ができます。
 
### <a name="example"></a>例
バックエンド Web アプリで、次の 2 つの GET 操作をサポートしているとします。 
*  `https://myappservice.azurewebsites.net/customer/{id}`
*  `https://myappservice.azurewebsites.net/customers`

`https://contosoapi.azureapi.net/store` のようなパスで、Web アプリを API Management サービスにインポートします。 次の表では、OpenAPI 仕様がある場合とない場合のそれぞれについて、API Management にインポートされる操作を示しています。 

| タイプ |インポートされる操作  |サンプルの要求 |
|---------|---------|---------|
|OpenAPI の仕様    | `GET  /customer/{id}`<br/><br/> `GET  /customers`         |  `GET https://contosoapi.azureapi.net/store/customer/1`<br/><br/>`GET https://contosoapi.azureapi.net/store/customers`       |
|ワイルドカード     | `GET  /*`         | `GET https://contosoapi.azureapi.net/store/customer/1`<br/><br/>`GET https://contosoapi.azureapi.net/store/customers`  |

ワイルドカード操作によって、OpenAPI 仕様の操作と同じ要求をバックエンド サービスに対して実行できます。 ただし、OpenAPI が指定された操作は、API Management で個別に管理できます。 

## <a name="prerequisites"></a>前提条件

+ 次のクイック スタートを完了すること:[Azure API Management インスタンスを作成する](get-started-create-service-instance.md)。
+ サブスクリプションに App Service があることを確認します。 詳細については、[App Service のドキュメント](../app-service/index.yml)を参照してください。

  サンプルの Web API を作成して Azure Web アプリとして公開する手順については、以下を参照してください。

    * [チュートリアル: ASP.NET Core で Web API を作成する](/aspnet/core/tutorials/first-web-api)
    * [Visual Studio Code で ASP.NET Core アプリを Azure に公開する](/aspnet/core/tutorials/publish-to-azure-webapp-using-vscode)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-backend-api"></a><a name="create-api"> </a>バックエンド API のインポートと発行

> [!TIP]
> 次の手順では、Azure portal で Azure API Management を使用してインポートを開始します。 アプリの **[API]** メニューから **[API Management]** を選択して、Web アプリから直接 API Management にリンクすることもできます。  

1. Azure portal で API Management サービスに移動し、メニューから **[API]** を選択します。
1. 一覧から **[App Service]** を選択します。

    :::image type="content" source="media/import-app-service-as-api/app-service.png" alt-text="App Service から作成する":::
1. **[参照]** をクリックして、サブスクリプション内の App Service の一覧を表示します。
1. App Service を選択します。 選択した Web アプリに OpenAPI 定義が関連付けられている場合、API Management によって取得およびインポートされます。 

    OpenAPI 定義が見つからない場合、API Management では、一般的な HTTP 動詞のワイルドカード操作を生成して API を公開します。 
1. API URL サフィックスを追加します。 サフィックスは、この API Management インスタンスでこの特定の API を識別する名前です。 この APIM インスタンス内で一意である必要があります。
1. API を成果物に関連付けることで API を公開します。 この場合、"*無制限*" の成果物が使用されます。 API を公開して開発者が利用できるようにするには、その API を成果物に追加します。 API の作成時に行うことも、後で設定することもできます。

    > [!NOTE]
    > 製品には、1 つまたは複数の API が関連付けられています。 多数の API を組み込み、開発者ポータルを通じてそれらを開発者に提供できます。 開発者は、まず製品をサブスクライブして API へのアクセス権を取得する必要があります。 サブスクライブすると、その製品の API に適したサブスクリプション キーを受け取ります。 APIM インスタンスを作成した場合は、既に管理者になっているため、既定ですべての製品をサブスクライブしています。
    >
    > すべての API Management インスタンスは、2 つのサンプル成果物を既定で備えています。
    > * **スターター**
    > * **無制限**   
1. 他の API 設定を入力します。 値は、作成時に設定することも、後で **[設定]** タブに移動して構成することもできます。設定については、「[最初の API のインポートと発行](import-and-publish.md#import-and-publish-a-backend-api)」のチュートリアルで説明されています。
1. **［作成］** を選択します
    :::image type="content" source="media/import-app-service-as-api/import-app-service.png" alt-text="App Service から API を作成する":::

## <a name="test-the-new-api-in-the-azure-portal"></a>Azure portal での新しい API のテスト

Azure Portal には、API の操作を表示およびテストするための便利な環境が用意されており、操作を直接呼び出すことができます。 [開発者ポータル](api-management-howto-developer-portal.md)で、または独自の REST クライアント ツールを使用して API をテストすることもできます。

1. 前の手順で作成した API を選びます。
1. **[テスト]** タブを選びます。
1. 操作を選択します。

    ページに、クエリ パラメーターのフィールドとヘッダーのフィールドが表示されます。 この API に関連付けられている成果物のサブスクリプション キーの場合、ヘッダーの 1 つは "Ocp-Apim-Subscription-Key" です。 API Management インスタンスを作成した場合は、既に管理者になっているので、キーが自動的に入力されます。 
1. **[送信]** をクリックします。

    テストが成功すると、バックエンドから **200 OK** といくつかのデータが応答として返されます。

### <a name="test-wildcard-operation-in-the-portal"></a>ポータルでワイルドカード操作をテストする

ワイルドカード操作が生成されるとき、操作がバックエンド API に直接マップしない場合があります。 たとえば、API Management にインポートされるワイルドカード GET 操作では、パス `/` を既定で使用します。 ただし、バックエンド API では、次のパスで GET 操作をサポートしている場合があります。

`/api/TodoItems`

パス `/api/TodoItems` は次のようにしてテストできます。 

1. 作成した API を選択し、操作を選択します。
1. **[テスト]** タブを選びます。
1. **[テンプレート パラメーター]** で、ワイルドカード (*) 名の横にある値を更新します。 たとえば、「`api/TodoItems`」と入力します。 この値が、ワイルドカード操作のパス `/` の後に追加されます。

    :::image type="content" source="media/import-app-service-as-api/test-wildcard-operation.png" alt-text="ワイルドカード操作のテスト":::
1. **[Send]** を選択します。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [公開された API の変換と保護](transform-api.md)
