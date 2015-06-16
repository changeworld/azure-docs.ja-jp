<properties 
	pageTitle="コードからの SaaS コネクタの使用" 
	description="Azure Marketplace から Azure のサブスクリプションにインストールした SaaS コネクタを構成する方法について説明します。" 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/07/2015" 
	ms.author="tdykstra"/>

# コードからの SaaS コネクタの使用

## 概要

このチュートリアルでは、モバイル アプリなどからプログラムを使用して呼び出すために、[Azure App Service](/documentation/services/app-service/) の[サービスとしてのソフトウェア (SaaS) コネクタ](../app-service-logic-what-are-bizTalk-api-apps.md)をインストール、構成、テストする方法について説明します。SaaS コネクタは、Office 365、Salesforce、Facebook、Dropbox などの SaaS プラットフォームとの対話を簡素化する [API アプリ](app-service-api-apps-why-best-platform.md)です。

たとえば、Dropbox アカウント内のファイルを読み書きするために HTTP 要求をコーディングする必要がある場合、Dropbox を直接操作するための認証プロセスは複雑になります。Dropbox コネクタを使用すれば複雑な認証の処理を避けることができるため、業務固有のコードの作成に集中できます。

> [AZURE.WARNING]**ロジック アプリから SaaS コネクタを使用する場合、ここでの指示に従う必要は*ありません***。ロジック アプリで SaaS コネクタを使用する方法の詳細については、「[新しいロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)」を参照してください。この記事は、*コード*を使用してコネクタを呼び出す場合を想定しています。
 
このチュートリアルでは例として DropBox コネクタを使用し、次の手順を順番に説明します。

* Azure サブスクリプションの[リソース グループ](../resource-group-overview.md)に Dropbox コネクタをインストールします。 
* Dropbox サービスに接続できるように、Dropbox コネクタを構成します (この手順を実行するには、Dropbox アカウントが必要になります)。
* 認証されたユーザーのみがリソース グループに含まれている API アプリにアクセスできるように、リソース グループを構成します。
* ユーザー認証と Dropbox 認証の両方が機能することをテストして確認します。

## Dropbox コネクタのインストール

1. [Azure プレビュー ポータル]のホーム ページに移動し、**[Marketplace]** をクリックします。

	![Azure プレビュー ポータルの Marketplace](./media/app-service-api-connnect-your-app-to-saas-connector/marketplace.png)

2. Dropbox を検索して、**[Dropbox コネクタ]** アイコンをクリックします。

	![Dropbox コネクタのクリック](./media/app-service-api-connnect-your-app-to-saas-connector/searchdb.png)
 
3. **[作成]** をクリックします。

	![Click Create](./media/app-service-api-connnect-your-app-to-saas-connector/clickcreate.png)
 
5. **[Dropbox コネクタ]** ブレードの **[App Service プラン]** の下にある **[新規作成]** をクリックし、**[新しい App Service プランの作成]** ボックスに「DropBoxPlan」と入力します。

	App Service プランの詳細については、「[Azure App Service プランの詳細な概要](azure-web-sites-web-hosting-plans-in-depth-overview.md)」を参照してください。

4. **[リソース グループ]** の下の **[新規作成]** をクリックし、**[新しいリソース グループの作成]** ボックスに「DropboxRG」と入力します。

	リソース グループの詳細については、[リソース グループを使用した Azure のリソースの管理](../resource-group-overview.md)に関するページを参照してください。

7. Free の**料金レベル**を選択します (一覧に表示されない場合は、**[すべて表示]** をクリックします。**[F1 Free]** をクリックした後に、**[選択]** をクリックします)。

	有料の料金レベルを使用できますが、このチュートリアルでは必要ありません。
 
11. 近くの**場所**を選択します。

9. <a id="gateway"></a>コネクタの**名前**は既定の "DropboxConnector" のままにして、**[作成]** をクリックします。

	![[作成] のクリック](./media/app-service-api-connnect-your-app-to-saas-connector/createdropbox.png)

	Azure App Service によってリソース グループが作成され、そのリソース グループに Dropbox コネクタ API のアプリと*ゲートウェイ* Web アプリが作成されます。このゲートウェイは、リソース グループのすべての API アプリへのアクセスを管理します。

	Azure プレビュー ポータルのホーム ページで **[通知]** をクリックして、リソースの作成の進行状況を確認することができます。

3. Azure によるコネクタの作成が完了したら、**[参照]、[リソース グループ]、[DropboxRG]** の順にクリックします。
 
	DropboxRG の **[リソース グループ]** ブレードに、このリソース グループのコネクタとゲートウェイが表示されます。

	![リソース グループの図](./media/app-service-api-connnect-your-app-to-saas-connector/rgdiagram.png)

## Dropbox アカウントと Dropbox コネクタの構成

Dropbox アカウントへの API のアクセスを有効にするには、Dropbox の開発者向けサイトで Dropbox アプリを作成する必要があます。クライアント ID とクライアント シークレットの値をその Dropbox アプリから Dropbox コネクタにコピーして、認証済みの要求のみを受け入れるようにコネクタを設定します。

### Dropbox アプリの作成

次の手順は、Dropbox.com サイトを使用して Dropbox アプリを作成するプロセスを示しています。Dropbox.com サイトは通知なしで変更される可能性があるため、実際の UI と異なる場合があります。

1. [Dropbox の開発者ポータル](https://www.dropbox.com/developers/apps)に移動し、**[App Console]**、**[Create App]** の順にクリックします。

	![Dropbox アプリの作成](./media/app-service-api-connnect-your-app-to-saas-connector/dbappcreate.png)

2. **[Dropbox API app]** を選択し、その他の設定を構成します。
 
	アカウント内にファイルがある場合は、次のスクリーン ショットに示すファイル アクセスのオプションによって、単純な HTTP Get 要求で Dropbox アカウントへのアクセスをテストすることができます。

	Dropbox API アプリの名前は、Dropbox サイトで許可されている内容にします。

3. **[Create app]** をクリックします。

	![Dropbox アプリの作成](./media/app-service-api-connnect-your-app-to-saas-connector/dbapiapp.png)

	次のページには、アプリ キーとアプリ シークレット設定 (Azure で名前を付けたクライアント ID とクライアント シークレット) が表示されます。これらは Azure Dropbox コネクタを構成するために使用します。

	このページにはリダイレクト URI を入力するフィールドもあります。その値は次のセクションで取得します。

	![Dropbox アプリの作成](./media/app-service-api-connnect-your-app-to-saas-connector/dbappsettings.png)

### Azure Dropbox コネクタへの Dropbox アプリ設定のコピーとその逆方向のコピー 

4. 別のブラウザー ウィンドウまたはタブで、[Azure プレビュー ポータル]に移動します。

3. Dropbox コネクタの **[API アプリ]** ブレードに移動します (まだ **[リソース グループ]** ブレードが表示されている場合は、図の Dropbox コネクタをクリックするだけです)。

4. **[設定]** をクリックし、**[設定]** ブレードの **[認証]** をクリックします。

	![[設定] のクリック](./media/app-service-api-connnect-your-app-to-saas-connector/clicksettings.png)

	![[認証] のクリック](./media/app-service-api-connnect-your-app-to-saas-connector/clickauth.png)

5. [認証] ブレードで、Dropbox サイトのクライアント ID とクライアント シークレットを入力し、**[保存]** をクリックします。

	![設定の入力と [保存] のクリック](./media/app-service-api-connnect-your-app-to-saas-connector/authblade.png)

3. **リダイレクト URI** (クライアント ID とクライアント シークレットの上にある灰色のボックス) の値をコピーして、前の手順で開いたままにしていたページにこの値を追加します。

	リダイレクト URI のパターンは次のとおりです。

		[gatewayurl]/api/consent/redirect/[connectorname]

	次に例を示します。

		https://dropboxrgaeb4ae60b7.azurewebsites.net/api/consent/redirect/DropboxConnector

	![リダイレクト URI の取得](./media/app-service-api-connnect-your-app-to-saas-connector/redirecturi.png)

	![Dropbox アプリの作成](./media/app-service-api-connnect-your-app-to-saas-connector/dbappsettings2.png)

### 認証されたアクセスを要求するための Dropbox コネクタの設定

既定では、コネクタの **[アクセス レベル] ** は **[内部] **に設定されています。つまり、同じリソース グループ内の他の API アプリと Web アプリからのみ呼び出すことができます。ただし、Dropbox では、認証されたユーザーにのみ Dropbox アカウントへのアクセスが許可されるため、ユーザー認証を要求するアクセス レベル設定に変更する必要があります。

1. **[設定]** ブレードに戻り、**[アプリケーション設定]** をクリックします。

2. **[アプリケーション設定]** ブレードで、**[アクセス レベル]** を **[パブリック (認証済み)]** に変更し、**[保存]** をクリックします。
	
	![[パブリック (認証済み)] に設定](./media/app-service-api-connnect-your-app-to-saas-connector/pubauth.png)

これで Dropbox コネクタの構成が完了し、発信呼び出しは Dropbox アカウントにアクセスできるようになり、着信呼び出しは認証されたユーザーから行うことが必須となりました。次のセクションではユーザーの認証に使用する認証プロバイダーを指定します。

## ゲートウェイの構成

[前](#gateway)に説明したように、ゲートウェイは、リソース グループのすべての API アプリへのアクセスを管理する特殊な Web アプリです。ユーザーを認証するようにゲートウェイをセットアップするには、Azure Active Directory、Google、Twitter などの認証プロバイダーを選択します。Dropbox コネクタを正常に呼び出すには、選択したプロバイダーによるユーザーの認証が必要になります。

- この手順を実行するには、[API アプリの保護](app-service-api-dotnet-add-authentication.md)に関するチュートリアルの[ゲートウェイの構成](app-service-api-dotnet-add-authentication.md#configure-the-gateway)のセクションに移動し、DropboxRG リソース グループ内のゲートウェイを構成する手順に従います。

## ユーザーと Dropbox の認証を検証するためのテスト

認証プロバイダーを使用するように DropboxRG リソース グループのゲートウェイを構成した後で、Dropbox コネクタをテストできます。

ほとんどの場合はコードから呼び出してコネクタを使用するため、その実現方法を説明するチュートリアルも作成されています。ただし、アプリの他の部分を接続する前に、コネクタが動作していることを検証する必要が生じる場合もあります。このチュートリアルでは、ブラウザーと単純な REST クライアント ツールを使用して、先ほどインストールして構成した Dropbox コネクタを通じて Dropbox サービスを操作できることを確認する方法を示します。

次の手順は、Chrome ブラウザー開発者ツールと Postman REST クライアント ツールを使用してこれらの手順を実行する方法を示しています。これはほんの一例であり、他のブラウザーやツールでも同じ手順を実行することができます。

### エンド ユーザーとしてのログイン

新しいブラウザー ウィンドウで次の手順に従います。使用する認証プロバイダーによって、プライベートまたは匿名でウィンドウを使用することが必要になる場合があります。

2. ゲートウェイと構成した認証プロバイダー用のログイン URL に移動します。URL のパターンは次のとおりです。 

    	http://[gatewayurl]/login/[providername]

	[Azure プレビュー ポータル]の **[ゲートウェイ]** ブレードで、ゲートウェイの URL を取得することができます (**[ゲートウェイ]** ブレードにアクセスするには、**[リソース グループ]** ブレードに表示される図内のゲートウェイをクリックします)。

	![ゲートウェイ URL](./media/app-service-api-connnect-your-app-to-saas-connector/gatewayurl.png)

	[providername] の値は、Facebook の場合は "facebook"、Twitter の場合は "twitter"、Azure Active Directory の場合は "aad" です。

	Azure Active Directory のサンプルのログイン URL を次に示します。

		https://dropboxrgaeb4ae60b7cb4f3d966dfa43.azurewebsites.net/login/aad/

3. ブラウザーにログイン ページが表示されたら資格情報を入力します。
 
	Azure Active Directory のログインを構成した場合は、admin@contoso.onmicrosoft.com など、[Azure ポータル]の [Azure Active Directory] タブで作成したアプリケーションの **[ユーザー]** タブに一覧表示されているいずれかのユーザーとしてログインします。

	ログインに成功すると、ログインが完了したことを示すページが表示されます。

	![](./media/app-service-api-connnect-your-app-to-saas-connector/logindone.png)

### Dropbox へのユーザーの ID の提供

Dropbox API を使用して Dropbox の承認を取得するには、Dropbox にユーザーの資格情報を提供する必要があります。これは Azure により自動的に行われますが、そのプロセスを開始するには、ブラウザーで専用の URL に移動する必要があります。この URL を取得するには、HTTP Post 要求をゲートウェイに送ります。

ゲートウェイへの HTTP Post 要求には、ログイン時に Azure から提供された認証トークンを含める必要があります。ブラウザーの要求の場合、トークンは Cookie に格納されているため、自動的に追加されますが、REST クライアント ツールを使用する HTTP Post 要求の場合は、Cookie からトークンを取得して HTTP Post 要求の要求ヘッダーに追加する必要があります。

1. ログインが完了したことを示すメッセージが表示されているブラウザー ウィンドウで、ブラウザーの開発者ツールに移動し、`x-zumo-auth` Cookie を検索します。次の手順で Cookie の値をコピーできるように、このウィンドウは開いたままにします。
 
	Chrome で Cookie の値を取得するには、次の手順に従います。

	- F12 キーを押して開発者ツールを開きます。
	- **[リソース]** タブに移動します。
	- ゲートウェイ サイトの Cookie を検索して、`x-zumo-auth` Cookie の **値**をトリプルクリックし、すべて選択します (Cookie の値をすべて取得したことを確認してください。ダブルクリックした場合、取得しているのは最初の部分のみである可能性があります)。  

	![トークンのコピー](./media/app-service-api-connnect-your-app-to-saas-connector/copytoken.png)

4. 新しいブラウザー タブまたはウィンドウで、ゲートウェイへの HTTP Post 要求を作成して送信し、同意の URL を要求します。HTTP ヘッダーとして `x-zumo-auth` トークンを含めます。

	URL のパターンは次のとおりです。

		[gatewayurl]/api/consent/list?api-version=2015-01-14&redirecturl=[a URL you want the browser to go to after you authenticate]

	次に例を示します。

		https://dropboxrgaeb4ae60b7cb4f3d966dfa43.azurewebsites.net/api/consent/list?api-version=2015-01-14&redirecturl=https://portal.azure.com

	Chrome の Postman で要求を送信するには、次の手順に従います。

	- 前に示した**要求 URL** を入力します。
	- メソッドを **Post** に設定します。
	- `x-zumo-auth` という名前のヘッダーを追加します。
	- ヘッダーの **[Value]** フィールドに、`x-zumo-auth` Cookie からコピーした値を貼り付けます。
	- **[Send]** をクリックします。
	 
	次の図に、Chrome の Postman ツールを示します。

	![同意 URL の送信](./media/app-service-api-connnect-your-app-to-saas-connector/sendforconsent.png)

	応答には、Dropbox でのユーザーの認証に使用する URL が含まれています (メソッドのドロップダウンを **Post** に設定したにもかかわらず Get メソッドがサポートされていないことを示すエラー応答が表示された場合は、別の REST クライアント ツールの使用が必要になることがあります。"Advanced REST Client" は、使用できる別の Chrome アドインです)。

	![同意 URL](./media/app-service-api-connnect-your-app-to-saas-connector/getconsenturl.png)

2. HTTP Post 要求への応答で受信した URL にアクセスします。

	Dropbox は、ユーザー ID を Dropbox API アプリと関連付けて、指定したリダイレクト URL (たとえば、例に従って https://portal.azure.com) を使用している場合は、Azure プレビュー ポータルになります) にブラウザーをリダイレクトします。

	ご使用の Dropbox アプリが開発者モードであるため、ブラウザーがリダイレクト URL に移動する前に、Dropbox のログイン ページが表示される可能性があります。Dropbox の資格情報を使用してログインした後で、ゲートウェイへのログインに使用したユーザー ID は Dropbox アプリに関連付けられるため、今後この Dropbox ログイン手順はそのユーザー ID では不要になります。

3. 次のセクションで使用するため、このブラウザ ウィンドウは開いたままにします。

### Dropbox コネクタ API の呼び出し 

Azure では次の 3 つの認証トークンが管理されるようになりました。

- Azure Active Directory など、ゲートウェイ用に構成した認証プロバイダーの認証トークン。
- Dropbox の認証トークン。
- Azure が作成した認証トークン ("zumo" トークン)。

Dropbox を操作するために HTTP 要求を行うときに使用する必要があるのは、zumo トークンのみです。バックグラウンドで Azure によって zumo トークンは他の 2 つと関連付けられます。Dropbox への呼び出しが行われるときに、コネクタがユーザーの代わりに他の 2 つを提供します。

次の手順では、Dropbox アカウント内のファイルを参照するために、Dropbox コネクタ への Get 要求を行います。Get 要求にはブラウザー ウィンドウを使用できるうえ、ブラウザー ウィンドウの Cookie には既に zumo トークンが格納されているため、URL に移動するだけで Dropbox のデータが返されます。

1. Azure プレビュー ポータルが表示されているブラウザー ウィンドウで、Dropbox コネクタ の **[API アプリ]** ブレードに戻ります。 

2. API アプリの URL をコピーします。
 
4. **[API の定義]** アイコンをクリックして、コネクタで使用可能な API メソッドを参照します。

	![[API アプリ] ブレード](./media/app-service-api-connnect-your-app-to-saas-connector/apiappblade.png)

	![API の定義](./media/app-service-api-connnect-your-app-to-saas-connector/apidef.png)

2. ゲートウェイで認証に使用したブラウザー ウィンドウで、フォルダーからファイル名を取得する GET メソッドを呼び出します。URL パターンを次に示します。

		[connectorurl]/folder/[foldername]
   
3. たとえば、コネクタの URL が https://dropboxrg784237ad3e7.azurewebsites.net のときにブログ フォルダー内のファイルを表示する必要がある場合、URL は次のようになります。

		https://dropboxrg784237ad3e7.azurewebsites.net/folder/blog

	API 呼び出しへの応答は、ブラウザーによってさまざまです。Chrome を使用している場合は、次の例のような応答が表示されます。

	![フォルダー要求への GET 応答](./media/app-service-api-connnect-your-app-to-saas-connector/dbresponse.png)

## 次のステップ

SaaS コネクタをインストール、構成、テストする方法を説明しました。詳細については、[コネクタの使用](../app-service-logic/app-service-logic-use-biztalk-connectors.md)に関するページを参照してください。

[Azure プレビュー ポータル]: https://portal.azure.com/
[Azure ポータル]: https://manage.windowsazure.com/
<!--HONumber=54--> 