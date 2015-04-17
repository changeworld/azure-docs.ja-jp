<properties 
	pageTitle="既存の API から API アプリへの変換" 
	description="Azure App Service の API アプリとしてデプロイするために Web API Visual Studio プロジェクトを構成する方法について説明します。" 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="tdykstra"/>

# 既存の API から API アプリへの変換

## 概要

このチュートリアルでは、Visual Studio で ASP.NET Web API プロジェクトを作成します。その後、Azure App Service の API アプリとしてそのプロジェクトを発行およびデプロイできる NuGet パッケージとプロジェクトのメタデータを追加します。また、API アプリのメタデータをカスタマイズする方法についても説明します。

[AZURE.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

## Web API プロジェクトの作成

1.  Visual Studio で **[新しいプロジェクト]** ダイアログを開きます。

2.  **[インストールされているテンプレート]** ウィンドウで **[クラウド]** ノードを選択します。次に、**[ASP.NET Web アプリケーション]** テンプレートを選択します。

3.  プロジェクトに「 *ContactsList*」という名前を付けて **[OK]** をクリックします。

	![New Project dialog](./media/app-service-dotnet-create-api-app-visual-studio/vstemplate.png)

4. **[新しい ASP.NET プロジェクト]** ダイアログで、**[空]** テンプレートを選択します。**[Web API]** チェック ボックスをオンにし、**[クラウドでホスト]** チェック ボックスをオフにしてから **[OK]** をクリックします。

	![Azure API App template](./media/app-service-dotnet-create-api-app-visual-studio/vstemplate2.png)

	Visual Studio で空の Web API プロジェクトのプロジェクト ファイルが作成されます。

	![Web API files in Solution Explorer](./media/app-service-dotnet-create-api-app-visual-studio/sewebapi.png)

3. **ソリューション エクスプローラー**で **[モデル]** フォルダーを右クリックし、コンテキスト メニューの **[追加]、[クラス]** の順にクリックします。 

	![](./media/app-service-dotnet-create-api-app-visual-studio/new-class-v2.png) 

4. 新しいファイルに *Contact.cs* と名前を付け、**[追加]** をクリックします。

5. 新しいファイルの内容を次のコードに置き換えます。 

		namespace ContactsList.Models
		{
			public class Contact
			{
				public int Id { get; set; }
				public string Name { get; set; }
				public string EmailAddress { get; set; }
			}
		}

5. **[コントローラー]** フォルダーを右クリックしてから、コンテキスト メニューの **[追加]、[コントローラー]** の順にクリックします。 

	![](./media/app-service-dotnet-create-api-app-visual-studio/new-controller.png)

6. **[スキャフォールドの追加]** ダイアログで、**[Web API 2 コントローラー - 空]** を選択し、**[追加]** をクリックします。 

	![](./media/app-service-dotnet-create-api-app-visual-studio/new-controller-dialog.png)

7. コントローラーに **ContactsController** という名前を付け、**[追加]** をクリックします。 

	![](./media/app-service-dotnet-create-api-app-visual-studio/new-controller-name.png)

8. 新しいコントローラー ファイルのコードを次のコードに置き換えます。 

		using ContactsList.Models;
		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Net;
		using System.Net.Http;
		using System.Threading.Tasks;
		using System.Web.Http;
		
		namespace ContactsList.Controllers
		{
		    public class ContactsController : ApiController
		    {
		        [HttpGet]
		        public IEnumerable<Contact> Get()
		        {
		            return new Contact[]{
		                new Contact { Id = 1, EmailAddress = "barney@contoso.com", Name = "Barney Poland"},
		                new Contact { Id = 2, EmailAddress = "lacy@contoso.com", Name = "Lacy Barrera"},
		                new Contact { Id = 3, EmailAddress = "lora@microsoft.com", Name = "Lora Riggs"},
		            };
		        }
		    }
		}


これで、Web API プロジェクトが機能するようになりました。プロジェクトが機能することを確認する最も簡単な方法は、ブラウザーから Get メソッドを呼び出すことです。

6. Ctrl キーを押しながら F5 キーを押してプロジェクトを実行します。

	ブラウザーには HTTP 403 エラーが表示されます。これは、Get メソッドをポイントする完全な URL を指定せずに開いているためです。

7. ブラウザーのアドレス バーで、URL に "api/contacts/get/" を追加してから Enter キーを押します。  最終的な URL は次の例のようになります。

		http://localhost:25735/api/contacts/get/

	ブラウザーによって、API 呼び出しに対する応答も異なります。Internet Explorer を使用している場合は、ブラウザーのウィンドウ下部にダウンロード メッセージが表示されます。

	![](./media/app-service-dotnet-create-api-app-visual-studio/get-response.png)

## API アプリとしてデプロイするためのアプリケーションの構成

1. **ソリューション エクスプローラー**で、プロジェクト (ソリューションではありません) を右クリックします。**[追加]、[Azure API App SDK]** の順にクリックします。

	![Add API app metadata](./media/app-service-dotnet-create-api-app-visual-studio/addapiappsdk.png)

2. **[API アプリのメタデータ ソースの選択]** ダイアログで、**[メタデータの自動生成]** をクリックします。

	![Choose automatic metadata](./media/app-service-dotnet-create-api-app-visual-studio/chooseswagger.png)

	この選択により、Swagger の動的 UI が有効になります。これについてはチュートリアルの後半で説明します。代わりに、Swagger の静的 API 定義ファイルを指定することもできます。これを行うには、**[Swagger のメタデータを含む静的 JSON ファイルを指定する]** を選択します。すると、Visual Studio からファイルのアップロードが促されます。アップロードするファイルは *Metadata* フォルダーに *apiDefinition.Swagger.json* という名前で保存されます。

3. **[OK]** をクリックします。

	Visual Studio によって *apiapp.json* ファイルと *Metadata* フォルダーが追加されます。

	![API App files in Solution Explorer](./media/app-service-dotnet-create-api-app-visual-studio/metadatainse.png)

	また、Swashbuckle NuGet パッケージも追加されます。この後の手順で、Swagger から提供される動的 API 定義 UI を試します。 

6. Ctrl キーを押しながら F5 キーを押してプロジェクトを実行します。

	前と同様、HTTP 403 エラーがブラウザーに表示されます。

7. ブラウザーのアドレス バーで、URL に "swagger/" を追加してから Enter キーを押します。  最終的な URL は次の例のようになります。

		http://localhost:25735/swagger/

8. [Swagger] ページの **[Contacts]** をクリックすると、使用できるメソッドが表示されます。
 
	`Get` のみ表示されていますが、これは、このメソッドが Contacts コントローラーで作成した唯一のメソッドであるためです。ページには、JSON の応答例が表示されます。

9. Get メソッドをクリックすると、JSON の応答例と **[Try it out]** ボタンが表示されます。

 	![Swagger - expand Contacts](./media/app-service-dotnet-create-api-app-visual-studio/swagger1.png)

9. **[Try it out]** をクリックします。

	ContactsController でコード化した応答が返されます。

 	![Swagger - Try it out](./media/app-service-dotnet-create-api-app-visual-studio/swagger2.png)

これで Web API プロジェクトを Azure App Service の API アプリとしてデプロイする準備ができました。このチュートリアルの以降のセクションでは、メタデータについて説明します。このメタデータを変更すると、API アプリをカスタマイズすることができます。 

## apiapp.json の確認

*apiapp.json* ファイルの設定によって、API アプリの識別方法および Azure Marketplace での表示方法が決定されます。このプレビュー リリースでは、Visual Studio に API アプリを Marketplace に発行する機能は含まれていないため、これらの設定の多くは効果がありません。

![apiapp.json in Solution Explorer](./media/app-service-dotnet-create-api-app-visual-studio/apiappjsoninse.png)

**[Azure API App SDK]** メニュー エントリを選択した場合に作成されるファイルの初期コンテンツは、次の例のようになります。

		{
		  "$schema": "http://json-schema.org/schemas/2014-11-01/apiapp.json#",
		  "id": "ContactsList",
		  "namespace": "",
		  "gateway": "2015-01-14",
		  "version": "1.0.0",
		  "title": "ContactsList",
		  "summary": "",
		  "author": "",
		  "endpoints": {
		    "apiDefinition": "/swagger/docs/v1",
		    "status": null
		  }
		}

次の表では、ファイルに含まれているフィールドと、フィールドに追加できるオプションのフィールドの形式と用途を説明しています。 

**注:** 前に説明したように、Azure Marketplace での API アプリの表示方法はフィールドによって決定されますが、Visual Studio には API アプリを Marketplace に発行する機能がまだ含まれていないため、このプレビュー リリースでは、これらのフィールドの多くは効果がありません。

| 名前 (太字のものは必須) | 型 | 説明 |
|:-----------|:------------|:------------|
|**id**           |string|このパッケージの ID。名前空間内で一意である必要があります。ピリオド、スラッシュ、または @ 文字を含めることはできません。プロジェクトをデプロイする際に、Visual Studio によって ID が一意であるかどうかが検証されます。また、ID を変更することができます。 
|**namespace**    |string|**id** プロパティと共に API アプリを一意に識別する名前空間。このプロパティは必須ですが、値は空の文字列にできます。 <br/><br/>このプロパティにより、contoso.com などのドメインを指定することができます。  使用するパッケージ ID が既に使用されている場合、ドメインを追加するとそのパッケージ ID を使用できます。たとえば、ContactsList が名前空間なしで既に API Apps ギャラリーに存在している場合は、contoso.com 名前空間を使用して ContactsList パッケージを追加することができます。 <br/><br/>ドメインを指定するもう 1 つの理由は、組織のメンバーのみがアクセスできる API Apps ギャラリーにパッケージを追加するためです。 <br/><br/>名前空間は、ピリオドをハイフンに、ハイフンを 2 つのハイフン (--) にそれぞれ変換した後で、発行元の名前として Marketplace で使用されます。<br/><br/>Microsoft が提供する API アプリの場合、名前空間は "microsoft.com" になります。  
|**version**      |string|[Semver](http://docs.nuget.org/Create/Versioning) 形式。1.0.1、1.1.0-alpha などです。
|**gateway**      |string|ゲートウェイのバージョン。2015-01-14 のように日付で表されます。*gateway* は特殊な Web アプリです。リソース グループ内の API アプリに対するすべての要求がゲートウェイを介してルーティングされます。ゲートウェイの主な機能の 1 つが認証の処理です。現時点では、利用できるゲートウェイのバージョンは 2015-01-14 のみです。今後、新しいゲートウェイのバージョンがリリースされたときに、このプロパティによって、互換性のない変更を回避し、前のゲートウェイ API を引き続き使用することができます。 
|**title**        |string|API アプリの表示名。
|**summary**      |string|API アプリの簡単な概要文。100 文字まで指定できます。
|description      |string|API アプリの全説明文。HTML 形式で 1500 文字まで指定できます。
|**author**       |string|API アプリの作成者。256 文字まで指定できます。
|homepage         |URI|API アプリのホーム ページ。
|endpoints        |object[]|API 定義エイドポイントを含めることができる 1 つの要素の配列。 
|>>endpoints.apiDefinition|string|動的 Swagger API 定義 UI の相対 URI ("/swagger/docs/v1" など)、または静的 Swagger API 定義ファイルの相対 URI。ASP.NET Web API には、通常、動的に生成される Swagger UI が最適な選択肢です。ただし、この UI が API で適切に機能しない場合、または ASP.NET Web API を使用していない場合は、静的な定義ファイルを指定できます。静的な定義ファイルを指定するには、そのファイルをポイントする相対 URI をこのプロパティに指定します。または、このプロパティを空のままにし、静的 API 定義ファイルを [apiDefinition.swagger.json](#apidef) としてメタデータ フォルダーに含めることもできます。 
|>>endpoints.status|URI|将来の使用に備えて予約されています。
|categories       |string[]|パッケージを Azure Marketplace 内のどの場所に表示するかを決定します。有効な値は social、enterprise、integration、protocol、app-datasvc、other です。既定値は other です。
|license          |object|API アプリのライセンス。
|>>**license.type**|string|SPDX ライセンスの ID。MIT などです。
|>>license.url    |url|ライセンスの全文をポイントする絶対 URL。
|>>license.requireAcceptance|bool|インストール前にライセンスが承認される必要があるかどうかを指定します。既定値は false です。
|links            |object[]|Marketplace のページに追加されるリンクの配列。
|>>**links.text** |string|リンクのテキスト。
|>>**links.url**  |url|リンクの URL。
|authentication|object[]|この API アプリが API 呼び出しを送信するために必要な認証の種類を示した配列。  たとえば、DropBox コネクタは DropBox に対して認証する必要があり、Salesforce コネクタは Salesforce に対して認証する必要があります。
|>>authentication.type|string|次の値がサポートされます (大文字と小文字を区別しません)。Box、DropBox、Facebook、GitHub、Google、Instagram、Marketo、Office365、OneDrive、Quickbooks、Salesforce、SharePointOnline、SugarCRM、Twitter、Yammer。ポータルは、この値に基づいて、クライアント ID、クライアント シークレットなどの必要な構成値を認識します。 
|>>authentication.scopes|string[]|認証の種類に固有のスコープの配列。
|copyright        |string|API アプリの著作権情報。
|brandColor       |string|UI エクスペリエンスを向上させるオプションのブランドの色。#abc、red など、CSS 互換の任意の形式を使用できます。
|tags             |string[]|パッケージに関連するタグの一覧。

<!--todo 依存関係についてまとめる準備ができたら追加
|dependencies    |object[]|パッケージの依存関係の配列。
|>>dependencies.id|string|依存関係パッケージの ID。
|>>dependencies.domain|string|依存関係パッケージのドメイン。
|>>dependencies.version|string|依存関係パッケージのバージョン。
-->

<!--todo 状態 URI についてまとめる準備ができたら追加
API アプリの現在の状態を示す値を返す Web サービスの Get メソッドの URI。この URI を指定した場合は、ポータルに、API アプリの現在の動作状態のほか、実行中であること、クォータに迫っていること、SSL 証明書の期限が切れそうなことなど、さまざまな情報が表示されます。ポータルで取得するときに予期される JSON の形式は、この表末尾のさらに後に示します。endpoints.status URI を指定しない場合は、ポータルには Azure プラットフォームの状態が API アプリの状態として表示されます。
以下の例は、 `endpoints.status` がポイントする Get メソッドからの予期される JSON 応答の形式を示しています。
		{
		  "status":[{ 
		    "name":"Nearing Quota", 
		    "message":"One or more quotas is nearing their limit", 
		    "level":"Warning" 
		  }, { 
		    "name":"Expiring Certificate", 
		    "message":"The SSL Certificate associated with this website is about to expire", 
		    "level":"Error"
		  }, {
		    "name":"Running", 
		    "message":"The website is handling traffic", 
		    "level":"Info"
		  }]
		}
 `name` プロパティは状態の短い記述であり、 `message` はそれより長い記述です。 `level` は通常の状態として、"エラー"、"警告"、"情報" のいずれかになります。
-->

## Metadata フォルダーの確認

Metadata フォルダーには、API Apps ギャラリーのアイコンとスクリーンショット、API を記述した Swagger ファイル、Azure ポータルの UI 構成を格納することができます。これらの情報はすべてオプションです。

<!-- todo: add later
an Azure Resource Manager template that specifies resources required, 
-->

![Metadata folder in Solution Explorer](./media/app-service-dotnet-create-api-app-visual-studio/metadatafolderinse.png)

**注:** 前に *apiapps.json* で説明しましたが、Visual Studio には API アプリを Marketplace に発行する機能が含まれていないため、このプレビュー リリースでは、Azure Marketplace での API アプリの表示方法に関するメタデータは効果がありません。

### Metadata/icons フォルダー

ギャラリーに表示されるアイコンを指定できます。 
カスタム アイコンを指定しない場合は、一般的な API アプリ用のアイコンが使用されます。アイコン ファイルは PNG 形式で、以下の名前とサイズの規則に従う必要があります。

<!--todo: also used in the workflow designer--> 

|ファイル名|サイズ
|:-----|:-----:
|small.png|40X40
|medium.png|90X90
|large.png|115X115
|hero.png|815X290
|wide.png|255X115

### Metadata/screenshots フォルダー

ギャラリーに表示するスクリーンショットを 5 個まで指定できます。画像ファイルは PNG 形式で、533x324 のサイズにする必要があります。

### <a id="apidef"></a>Metadata/apiDefinition.swagger.json ファイル

[Swagger 2.0](https://github.com/swagger-api/swagger-spec/blob/master/versions/2.0.md) 形式のファイルを使用して API アプリの API 定義を記述することができます。これにより、パッケージに API 定義を静的に公開できます。 

<!--todo Explain why this is useful. Original text:
Static API definitions are required for workflow designers to understand the API App triggers and actions before provisioning.-->

<!-- todo: これの代わりになるものがある場合はチェック (おそらく deploymentTemplates サブフォルダー)。
### resourceTemplate.delta.json ファイル
API アプリのデプロイ中に実行されるカスタム Azure リソース マネージャー テンプレートを指定できます。この差分ファイルで指定されたリソースは、API アプリによって既定で作成されるリソースに追加されます。たとえば API アプリに SQL Database インスタンスが必要な場合は、このファイルを使用して、API アプリのデプロイ時にデータベースが自動的にプロビジョニングされ、  接続文字列が構成設定に設定されるようにすることができます。
-->  

### Metadata/UIDefinition.json ファイル

ヒントや検証などの UI 情報を、[Azure
Marketplace 形式](https://auxdocs.azurewebsites.net/ja-jp/documentation/articles/gallery-items)で指定できます。

### Metadata/deploymentTemplates フォルダー

**[Azure API アプリ SDK]** メニュー エントリを選択した場合に、Visual Studio によって、このフォルダーが作成されます。ただし、このプレビュー リリースでは使用されません。

## 次のステップ

これで、Web API プロジェクトを API アプリとしてデプロイする準備ができました。デプロイする手順については、[API アプリのデプロイ](app-service-dotnet-deploy-api-app.md)に関するチュートリアルを参照してください。

詳細については、「[API Apps とは](app-service-api-apps-why-best-platform.md)」を参照してください。

<!--HONumber=49-->