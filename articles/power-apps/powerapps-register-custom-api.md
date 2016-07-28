<properties
	pageTitle="ロジック フローと PowerApps でカスタム API を使用する方法 | Microsoft Azure"
	description="カスタム API とは何か、OAuth プロバイダーの使用、Swagger を使用して PowerApps とロジック フローにカスタム API を追加する方法について説明します"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="sunaysv"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="07/12/2016"
   ms.author="mandia"/>

# カスタム API とは

カスタム API は、PowerApps とロジック フローに組み込んで使用できる RESTful API です。これらの API は、[OpenAPI][1] 標準に準拠している、明確に文書化された仕様が入手可能であれば、任意の場所でホストできます。

>[AZURE.IMPORTANT] このトピックは、powerapps.microsoft.com の「[What are custom APIs (カスタム API とは)](https://powerapps.microsoft.com/tutorials/register-custom-api/)」に移動しました。最新版については PowerApps をご覧ください。この Azure のリンクはアーカイブされます。

## はじめにやるべきこと

- カスタム API は、PowerApps のユーザーであればだれでも使用できます。PowerApps Enterprise は必要ありません。
- カスタム API の Swagger ファイル (.json) とアイコンが必要です。このトピックでは、Swagger ファイルを作成するための方法をいくつか説明します。アイコンには任意の画像を使用できます。


## 認証

次の認証メカニズムのいずれかを使用できます。

- 基本認証
- OAuth 2.0: カスタム API と共に使用できる、サポート対象の OAuth 2.0 プロバイダーすべてを次に示します (追加のプロバイダーのサポートが近日提供される予定です)。

	- Azure Active Directory
	- Box
	- Dropbox
	- Facebook
	- Google
	- Instagram
	- OneDrive
	- SalesForce
	- Slack
	- Yammer

> [AZURE.NOTE] API キー認証のサポートは近日提供される予定です。


OpenAPI (Swagger) ドキュメントで認証の種類を指定する方法の詳細については、「[OpenAPI Specification (OpenAPI 仕様)][1]」をご覧ください。

認証されていないアクセスを API エンドポイントで許可する場合は、OpenAPI (Swagger) ファイルから ```securityDefintions``` オブジェクトを削除する必要があります。次の例では、```securityDefintions``` オブジェクト全体を削除します。

```
  "securityDefinitions": {
    "AAD": {
      "type": "oauth2",
      "flow": "implicit",
      "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
      "scopes": []
    }
  },
```

> [AZURE.TIP] .json ファイルではコメントを追加できません。すべてのテキストがデータの一部と見なされます。

### 認証の例
* AAD 認証を使用した [Azure Resource Manager](powerapps-azure-resource-manager-tutorial.md)
* AAD 認証を使用した [Azure Web アプリ](powerapps-web-api-tutorial.md)

## カスタム API の登録

### 手順 1. Swagger ファイルを作成する

Swagger ファイルは、次のような**任意**の API エンドポイントから作成できます。

- 発行され、公開されているすべての API。たとえば、[Spotify][2]、[Uber][3]、[Slack][4]、[Rackspace][5] などです。
- 作成して、Web アプリをデプロイできるクラウド サービス (アマゾン ウェブ サービス (AWS)、Heroku、Azure Web Apps、Google Cloud など) にデプロイした API。
- ネットワーク内または自身のコンピューター上にデプロイされた API (ただし、API がインターネット上に公開されている場合に限ります)。

Swagger ファイルを作成すると、.json ファイルが作成されます。この .json ファイルは手元に保存しておいてください。

#### Swagger ファイルの作成に関するサポート情報

- Swagger ファイルの作成に関する知識がない場合やこれまで Swagger ファイルを作成したことがない場合は、[Swagger の概要][6]に関するページでわかりやすく解説されています。
 
- 独自の API を作成して Azure にデプロイし、この新しい API に基づいて Swagger ファイルを作成するには、[Web API チュートリアル](powerapps-web-api-tutorial.md)の使用を検討してください。このチュートリアルでは、Swagger ファイルの操作について説明しています。また、GitHub には [Hello World のサンプル][7]も用意されています。

- Swagger ファイルを検証するには、[Swagger エディター][8]を使います。.json データを貼り付けると、検証が自動的に行われます。

- PowerApps と Logic Flows で使用できるように Swagger ドキュメントをカスタマイズするには、[Swagger 定義のカスタマイズ](powerapps-how-to-swagger.md)に関するページをご覧ください。

### 手順 2. カスタム API への接続を追加する
これでカスタム API 用の Swagger ファイル (.json ファイル) が生成されたので、PowerApps への接続を追加します。カスタム API のアイコンも必要になります。

1. PowerApps [Web ポータル][9]に移動し、職場アカウントでサインインします。

	> [AZURE.NOTE] 現時点では、カスタム API は PowerApps Web ポータルでのみ使用できます。PowerApps クライアントでは使用できません。

2. **[Connections (接続)]**、**[Add a connection (接続の追加)]** の順に選択します。  
	![](./media/powerapps-register-custom-api/createnewconnection.png "Create Custom API")  

3. **[Connect to custom API (カスタム API に接続)]** を選択します。  
	![](./media/powerapps-register-custom-api/connecttocustomapi.png "Create Custom API")  
	json ファイルやアイコン ファイルなど、API のプロパティを追加します。**[Next (次へ)]** をクリックします。  

	|プロパティ|説明|
|---|---|
|名前|カスタム API の名前を入力します。これがサンプル Web API である場合、**MySampleWebAPI** という名前を付けることができます。|
|Swagger API definition (Swagger API の定義)|Swagger から作成された json ファイルを参照します。|
|Upload API icon (API アイコンのアップロード)|アイコン ファイルを参照します。|
|説明|カスタム API の説明を入力します。これがサンプル Web API である場合は、「**サンプルの Web API チュートリアル**」と入力できます。|

4. 認証プロパティを入力します。.json ファイルの ```securityDefintions``` オブジェクトで OAuth2 認証が使用されている場合は、次の値の入力を求められます。

	|プロパティ|説明|
|---|---|
|クライアント ID|サポート対象の OAuth ID プロバイダー (このトピックにある一覧を参照) のいずれかを使用している場合は、クライアント ID が提供されます。このクライアント ID を入力します。
|クライアント シークレット|選択した ID プロバイダーから入手したクライアント シークレットを入力します。|  

	このトピックの「**認証の例**」では、これらの OAuth プロパティの例を紹介しています。

	.json ファイルで ```securityDefintions``` オブジェクトが使用されていない場合、追加の値は必要ありません。

5. **[作成]** を選択します。これで、カスタム API が **[Available Connections (利用可能な接続)]** に表示されます。  

	![](./media/powerapps-register-custom-api/mycustomapi.png "Available connections")  


> [AZURE.TIP] Swagger ファイルの検証に失敗した場合は、余分な文字が存在する可能性があります。たとえば、Web サイトなど、ほとんどすべてのデータは引用符で囲まれている必要があります。そのため、`https://mywebapi.mywebsite.com` を引用符で囲んでいない場合、ファイルの検証は失敗します。


### 手順 3. ロジック フローと PowerApp にカスタム API を追加する
これで、カスタム API を PowerApp またはロジック フローで使用する準備が整いました。このセクションでは、カスタムの Weather API を使用します。

#### ロジック フローにカスタム API を追加する
この手順では、非常に簡単なロジック フローを作成し、カスタム API を追加する方法を説明します。詳細については、[Logic Flows の概要][10]に関するページをご覧ください。

1. PowerApps [Web ポータル][9]で **[Home (ホーム)]** タブを選択します。
2. **[Make a logic flow (ロジック フローの作成)]** で **[Get started (作業の開始)]** を選択します。
3. このウィンドウには、あらかじめ作成された、一般的なシナリオを使用するロジック フロー テンプレートがいくつかあります。これらのテンプレートのいずれかを使用し、そのテンプレートにカスタム API を追加できます。また、**[Create from blank (新規作成)]** を選択して、ロジック フローを一から作成することもできます。

	カスタム API を追加する最も簡単な方法は、**[Create from blank (新規作成)]** を選択することです。これを選択すると、次のロジック フローが開きます。  
	![](./media/powerapps-register-custom-api/createfromblank.png "Start of Logic Flow")   

4. **[Recurrence (繰り返し)]** を選択し、頻度を 1 分に設定します。    
	![](./media/powerapps-register-custom-api/logicrecurrence.png "Select Recurrence")  	

5. プラス記号 (![](./media/powerapps-register-custom-api/flowplussign.png)) を選択し、**[Add an action (アクションの追加)]** を選択します。一覧に、カスタム API が表示されます。  
![](./media/powerapps-register-custom-api/logicflow.png "Your custom API") 

次に続くステップは、API で実行できる処理によって決まります。おそらく、天気の例では、API は現在の気温を取得した後、Office 365 を使用して電子メールを送信します。

![](./media/powerapps-register-custom-api/logicflowexample.png "Weather example") 



#### PowerApp にカスタム API を追加する
この手順では、非常に簡単な PowerApp を作成し、カスタム API を追加する方法を説明します。詳細については、[データからのアプリの作成][11]に関するページをご覧ください。

> [AZURE.NOTE] 現時点では、カスタム API は PowerApps Web ポータルでのみ使用できます。PowerApps クライアントでは使用できません。

1. PowerApps [Web ポータル][9]で、**[New PowerApp (新しい PowerApp)]** を選択します。  
	![](./media/powerapps-register-custom-api/newpowerapp.png "Select New PowerApp")  
2. ブラウザーで新しいタブが開きます。この新しいタブに、空の PowerApp が自動的に作成されます。**[connect to data (データに接続)]** を選択します。  
![](./media/powerapps-register-custom-api/blankpowerapp.png "Select connect to data")  
3. **[Content (コンテンツ)]** タブの **[Data sources (データ ソース)]** を選択します。  
![](./media/powerapps-register-custom-api/datasources.png "Select connect to data")  
4. 新しい画面の **[My connections (マイ接続)]** で、対象のカスタム API を選択します。  
![](./media/powerapps-register-custom-api/screencustomapi.png "Select your custom API")  
5. **[Add data source (データ ソースの追加)]** を選択します。

カスタム API は、追加されると、関数バー、テキスト ボックスなどで使用できます。たとえば、関数バーで、「**MySampleWebAPI**」の入力を開始すると、利用可能な関数が表示されます。[Office 365 のデータの表示][12]に関するページには、Office 365 API の使用例が示されています。


## カスタム API の共有
カスタム API は、ユーザーどうしで共有できます。カスタム API を追加したら、**[Connections (接続)]** タブ、**[Custom APIs (カスタム API)]** の順に選択し、共有アイコンを選択します。

![](./media/powerapps-register-custom-api/sharecustomapi.png "Share Custom API")

> [AZURE.NOTE] カスタム API を共有できるのは、所属する組織内の他のユーザーのみです。

## クォータとスロットル

- 1 つの PowerApps アカウントに作成できるカスタム API は最大 5 つです。共有している他のユーザーのカスタム API はこのクォータに含めません。
- カスタム API で作成された各接続に対して、ユーザーは 1 分間に最大 500 個の要求を実行できます。
- カスタム API を削除すると、その API に対して作成された接続がすべて削除されることに注意してください。

カスタム API に関するご質問やご意見があれば、[customapishelp@microsoft.com](mailto:customapishelp@microsoft.com) まで電子メールでお送りください。

<!--Reference links in article-->
[1]: https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md#securityDefinitionsObject
[2]: https://developer.spotify.com/
[3]: https://developer.uber.com/
[4]: https://api.slack.com/
[5]: http://docs.rackspace.com/
[6]: http://swagger.io/getting-started/
[7]: https://github.com/OAI/OpenAPI-Specification/wiki/Hello-World-Sample
[8]: http://editor.swagger.io/#/
[9]: https://web.powerapps.com
[10]: https://powerapps.microsoft.com/tutorials/get-started-logic-flow/
[11]: https://powerapps.microsoft.com/tutorials/get-started-create-from-data/
[12]: https://powerapps.microsoft.com/tutorials/show-office-data/

<!------HONumber=AcomDC_0713_2016-->