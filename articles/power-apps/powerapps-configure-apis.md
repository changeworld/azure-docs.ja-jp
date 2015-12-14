<properties
	pageTitle="Azure ポータルでの PowerApps API プロパティの変更または更新 | Microsoft Azure"
	description="カスタム アイコンの追加、XML ポリシーの更新、または PowerApps API の Swagger 定義の更新"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="guayan"/>

# 既存 API とプロパティの更新

App Service 環境で登録した API とは、本質的にはバックエンド サービスのプロキシです。API を作成したあとで、そのプロパティを変更する場合があります。たとえば、次のような変更があります。

- API にカスタム アイコンを追加する。
- API によって使用されるバックエンドを保護する方法を変更する。 
- API の表示名をよりユーザー フレンドリーな名前に更新する。


#### 開始のための前提条件

- [PowerApps Enterprise](powerapps-get-started-azure-portal.md) へのサインアップ。
- [アプリのサービス環境](powerapps-get-started-azure-portal.md)の作成。
- ユーザー環境への [API](powerapps-register-from-available-apis) 登録。

## カスタム アイコンの追加またはユーザー フレンドリー表示名の追加

1. [Azure ポータル](https://portal.azure.com)で、API のブレードを開きます。
2. **[すべての設定]** を選択します。
3. **[設定]** で**[全般]** を選択します: ![][11]

[全般] では次の設定を変更できます。

設定 | 説明
--- | ---
表示名 | この名前は PowerApps で*利用可能な接続*を一覧表示する際に使用します。既定では、API のリソース名を使用するので、PowerApps ユーザーにはわかりやすい名前になっていない可能性があります。ユーザー フレンドリーな表示名を入力できます。たとえば、**新規顧客の注文** または **営業履歴の表示**などと名前をつけることができます。  
アイコン URL | 自身の API にカスタム アイコンを追加できます。このアイコンは PowerApps で*利用可能な接続*と*接続*の一覧表示で使用します。既定では、次のアイコンが使用されます。: <br/><br/>![][12] <br/><br/>カスタム アイコンを使用する際は次のことに注意してください。:<br/><ul><li>アイコンへの URL はパブリックにアクセスできるようにする必要があります。</li><li>ファイルの拡張子は .png または .svg を使用できます。png ファイルを使用する場合は、40 x 40 ピクセルをお勧めします。</li></ul>
URL スキーム | API がサポートする 1 つまたは複数のスキームを選択します。**HTTP**、**HTTPS**、または **HTTP および HTTPS** から選択できます。既定では、HTTP および HTTPS が可能です。<br/><br/> App Service 環境はバックエンド構成に基づいてスキームを自動的に構成します。そのため、追加で何らかの構成を行う必要があれば、バックエンド サービスを開発または変更できます。 
バックエンド サービスによる認証 | App Service 環境でバックエンド サービスを登録後、クライアントが API を使用してのみバックエンド サービスを呼び出すよう、バックエンドをセキュリティ保護することをお勧めします。バックエンドがどこでデプロイされたかに基づいて、次のオプションが使用可能です。:<br/><br/><ul><li><strong>この API を介してのみアクセス可能</strong>: このオプションはバックエンドが App Service 環境でデプロイされた場合にのみ使用可能です。これを選択すると、バックエンド上のホスト名はいずれも無効になります。API プロキシも同様に同じ App Service 環境で実行されているため、バックエンドには変わらずアクセスできます。</li><li><strong>HTTP 基本認証</strong>: このオプションはバックエンドがどこでデプロイされたかに関係なく使用可能です。これが選択されている場合は、ユーザー名とパスワードを入力します。プロキシがバックエンドを呼び出すと、プロキシは HTTP 基本認証を使用して、HTTP 承認ヘッダーのユーザー名とパスワードを渡します。最終的に、バックエンド サービスが入力されたユーザー名とパスワードを確認 (認証) する必要があります。<br/><br/>ASP.NET Web API 2 での HTTP 基本認証の実装についての詳細は、「[ASP.NET Web API 2 での認証フィルター](http://www.asp.net/web-api/overview/security/authentication-filters)」を参照してください。</li></ul>


## API の Swagger の更新

1. [Azure ポータル](https://portal.azure.com)で、API のブレードを開きます。
2. **[すべての設定]** を選択します。
3. **[設定]** で、**[API 定義]** を選択します。: ![][13]

**Swagger 2.0** はサポートされている API 定義の形式です。現在の API の定義は、埋め込み JSON エディターで書かれています。インラインで編集したり、新しい JSON ファイルをアップロードしたりできます。変更を**保存**したら、API 定義で発生したエラーを含むいかなるエラーもこのブレード上に表示されます。

- Swagger 2.0 の詳細については、「[公式の Swagger web サイト](http://swagger.io)」を参照してください。
- API を開発する際に Swagger 2.0 を取得する方法の詳細については、次のページを参照してください。  
	- [Azure App Service での ASP.NET API アプリの作成](../app-service-dotnet-create-api-app.md)
	- [Azure App Service での Java API アプリの構築とデプロイ](../app-service-api-java-api-app.md)
	- [Azure App Service での Node.js API アプリの構築とデプロイ](../app-service-api-nodejs-api-app.md)
	- [Swashbuckle が生成する API 定義をカスタマイズする](../app-service-api-dotnet-swashbuckle-customize.md)
- PowerApps のための Swagger 2.0 を使用するベスト プラクティスについての詳細は、「[PowerApps 用 API の開発](powerapps-develop-api.md)」を参照してください。

## API の XML ポリシーの更新

1. [Azure ポータル](https://portal.azure.com)で、API のブレードを開きます。
2. **[すべての設定]** を選択します。
3. **[設定]** で、**[ポリシー]** を選択します。: ![][14]

このポリシーは [Azure API Management](https://azure.microsoft.com/services/api-management) でサポートされるポリシーと同じです。現在のポリシーは、組み込みの XML エディターで書かれています。インラインでの編集、または新しい XML ファイルのアップロードが可能です。変更を**保存**したら、API ポリシーで発生した問題を含むいかなるエラーもこのブレード上に表示されます。

「[Azure API Management のポリシー](../api-management-howto-policies.md)」は、ポリシーの構成や理解のための学習には適切なリソースとなっています。


## まとめと次のステップ
API を作成後、このトピックの手順を使用して設定変更をしたり、設定の一部をカスタマイズしたりすることも可能です。

PowerApps の詳細については、次の関連するトピックやリソースも参照してください。

- [API を構成して AAD 保護バックエンドに接続する](powerapps-configure-apis-aad.md)
- [PowerApps 用 API の開発](powerapps-develop-api.md)

[11]: ./media/powerapps-configure-apis/api-settings-general.png
[12]: ./media/powerapps-configure-apis/api-default-icon.png
[13]: ./media/powerapps-configure-apis/api-settings-api-definition.png
[14]: ./media/powerapps-configure-apis/api-settings-policy.png

<!---HONumber=AcomDC_1203_2015-->