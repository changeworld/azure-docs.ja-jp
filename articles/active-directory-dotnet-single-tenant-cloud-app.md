<properties linkid="" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Azure Active Directory によるシングルテナント クラウド アプリケーションの開発" authors="" solutions="" manager="" editor="" />



# Azure Active Directory によるシングルテナント クラウド アプリケーションの開発

<h2><a name="introduction"></a>はじめに</h2>

このチュートリアルでは、Azure Active Directory を活用して、ディレクトリ ユーザーを認証し、ディレクトリからそれらのユーザーのデータを読み取る方法について説明します。学習内容: 

* 顧客のテナントでの Web アプリケーションのプロビジョニング
*  WS-Federation を使用したアプリケーションの保護
* Graph API を使用したディレクトリへのアクセス

<h3>前提条件</h3>
次の開発環境の前提条件はこのチュートリアルで必要になります。

* Visual Studio 2010 SP1
* Microsoft .NET Framework 4.0
* [ASP.NET MVC 3]
* [Windows Identity Foundation 1.0 ランタイム]
* [Windows Identity Foundation 3.5 SDK]
* [WCF Data Services for OData V3]
* インターネット インフォメーション サービス (IIS) 7.5  (SSL が有効)
*  Windows PowerShell
* [Office 365 PowerShell コマンドレット]

<h3>目次</h3>
* [はじめに][]
* [手順 1. ASP.NET MVC アプリケーションを作成する][]
* [手順 2. 会社のディレクトリ テナントでアプリケーションをプロビジョニングする][]
* [手順 3. WS-Federation を従業員のサインインに使用してアプリケーションを保護する][]
* [手順 4. Graph API を使用してディレクトリ データを読み取る][]
* [まとめ][]

<h2><a name="createapp"></a>手順 1. ASP.NET MVC アプリケーションを作成する</h2>
この手順では、リソースが保護されたシンプルな ASP.NET MVC アプリケーションを作成する方法について説明します。このリソースへのアクセスは、会社の STS によって管理されるフェデレーション認証を使用して許可されます (後で説明)。

1.  Visual Studio を管理者として開きます。
2. **[ファイル]** メニューの **[新しいプロジェクト]** をクリックします。
3. 左側のテンプレート メニューで **[Web]** を選択し、**[ASP.NET MVC 3  Web アプリケーション]** をクリックします。プロジェクトに「*OrgIdFederationSample*」という名前を付けます。
4. **[新しい ASP.NET MVC 3  プロジェクト]** ダイアログ ボックスで **[空]** テンプレート、**[OK]** の順にクリックします。
5. Visual Studio によって新しいプロジェクトが生成された後、**ソリューション エクスプローラー**で **Controllers** フォルダーを右クリックし、**[追加]**、**[コントローラー]** の順にクリックします。
6. **[コントローラーの追加]** ダイアログ ボックスで、新しいコントローラーに「*HomeController.cs*」という名前を付け、**[追加]** をクリックします。
7. **HomeController.cs** ファイルが作成されて、開かれます。コード ファイルで ***Index()*** メソッドを右クリックし、**[ビューの追加]** をクリックします。
8. **[ビューの追加]** ダイアログ ボックスで、**[OK]** をクリックします。**Home** という新しいフォルダーに、**Index.cshtml** ファイルが作成されます。
9. **ソリューション エクスプローラー**で **OrgIdFederationSample** プロジェクトを右クリックし、**[プロパティ]** をクリックします。
10. プロパティ ウィンドウの左側のメニューで **[Web]** をクリックし、**[IIS Web サーバーの使用]** を選択します。プロジェクトの仮想ディレクトリの作成を求めるダイアログ ボックスが表示される場合があります。このダイアログ ボックスで **[はい]** をクリックします。
11. アプリケーションをビルドし、実行します。Home コントローラーの Index ページが表示されます。

<h2><a name="provisionapp"></a>手順 2. 会社のディレクトリ テナントでアプリケーションをプロビジョニングする</h2>
この手順では、Azure Active Directory 顧客の管理者がテナントで MVC アプリケーションをプロビジョニングし、シングル サインオンを構成する方法について説明します。この手順の完了後、会社の従業員は Office 365  アカウントを使用して Web アプリケーションに対して認証できるようになります。

プロビジョニング プロセスは、アプリケーション用に新しいサービス プリンシパルを作成することから始まります。サービス プリンシパルは Azure Active Directory によって使用されて、アプリケーションがディレクトリに対して登録および認証されます。

1. Office 365  PowerShell コマンドレットをダウンロードしてインストールします (まだインストールしていない場合)。
2. **[スタート]** メニューから **Microsoft Online Services Module for Windows PowerShell ** コンソールを実行します。このコンソールには、Office 365  テナントの属性を構成するためのコマンド ライン環境が用意されています。たとえば、サービス プリンシパルの作成や変更などが可能です。
3. 必要な **MSOnlineExtended** モジュールをインポートするには、次のコマンドを入力し、Enter キーを押します。

		Import-Module MSOnlineExtended –Force
4. Office 365  ディレクトリに接続するには、会社の管理者の資格情報を指定する必要があります。次のコマンドを入力して Enter キーを押したら、プロンプトで資格情報を入力します。

		Connect-MsolService
5. この時点でアプリケーション用に新しいサービス プリンシパルを作成します。次のコマンドを入力し、Enter キーを押します。

		New-MsolServicePrincipal -ServicePrincipalNames @("OrgIdFederationSample/localhost") -DisplayName "Federation Sample Web Site" -Type Symmetric -Usage Verify -StartDate "12/01/2012" -EndDate "12/01/2013" 
この手順では、次のような出力が表示されます。

		The following symmetric key was created as one was not supplied qY+Drf20Zz+A4t2w e3PebCopoCugO76My+JMVsqNBFc=
		DisplayName           : Federation Sample Web Site
		ServicePrincipalNames : {OrgIdFederationSample/localhost}
		ObjectId              : 59cab09a-3f5d-4e86-999c-2e69f682d90d
		AppPrincipalId        : 7829c758-2bef-43df-a685-717089474505
		TrustedForDelegation  : False
		AccountEnabled        : True
		KeyType               : Symmetric
		KeyId                 : f1735cbe-aa46-421b-8a1c-03b8f9bb3565
		StartDate             : 12/01/2012 08:00:00 a.m.
		EndDate               : 12/01/2013 08:00:00 a.m.
		Usage                 : Verify 
	<div class="dev-callout"><strong>注</strong><p>この出力のうち特に生成された対称キーを保存しておく必要があります。このキーはサービス プリンシパルの作成時にしか表示されず、以降に取得することはできません。その他の値は、グラフ API を使用してディレクトリ内の情報を読み書きするために必要です。</p></div>

6. 最後の手順では、アプリケーションの応答 URL を設定します。応答 URL は、認証の試行後に応答が送信される場所です。次のコマンドを入力し、Enter キーを押します。

		$replyUrl = New-MsolServicePrincipalAddresses -Address "https://localhost/OrgIdFederationSample" 

		Set-MsolServicePrincipal -AppPrincipalId "7829c758-2bef-43df-a685-717089474505" -Addresses $replyUrl 
	
これで、Web アプリケーションはディレクトリでプロビジョニングされ、会社の従業員が Web シングル サインオンに使用できるようになりました。

<h2><a name="protectapp"></a>手順 3. WS-Federation を従業員のサインインに使用してアプリケーションを保護する</h2>
この手順では、Windows Identity Foundation (WIF) を使用して、フェデレーテッド ログインのサポートを追加する方法について説明します。また、ログイン ページを追加し、アプリケーションとディレクトリ テナントとの信頼を構成します。

1. Visual Studio で **OrgIdFederationSample** プロジェクトを右クリックし、**[STS 参照の追加]** をクリックします。このコンテキスト オプションは、WIF SDK のインストール時に追加されます。
2. **[フェデレーション ユーティリティ]** ダイアログ ボックスの **[アプリケーション URI]** に、次の形式で URI を入力します。

		spn:<Your AppPrincipalId>@<Your Directory Domain>

	**spn** は、この URL がサービス プリンシパル名であることを指定しています。**Your AppPrincpalId** はサービス プリンシパルの作成時に生成される **AppPrincipalId** GUID 値、**Your Directory Domain** はディレクトリ テナントの *.onmicrosoft.com ドメインです。このサンプル アプリケーションでは、アプリケーションの URI 全体の値を次のように指定します。

		spn:7829c758-2bef-43df-a685-717089474505@awesomecomputers.onmicrosoft.com

	アプリケーション URI を入力した後、**[次へ]** をクリックします。

3. "アプリケーションが、セキュリティで保護された https 接続でホストされていない" ことを知らせる警告ダイアログ ボックスが表示されます。これは **spn:** 形式を認識しないフェデレーション ユーティリティによって発生する警告ですが、この警告が表示されても、アプリケーションはセキュリティで保護された https 接続を使用します。**[はい]** をクリックして続行します。

4. フェデレーション ユーティリティの次のページで **[既存の STS を使用する]** を選択し、**[WS-Federation メタデータのドキュメントの場所]** に WS-Federation メタデータ ドキュメントの URL を入力します。この URL は、次の形式で指定します。

		https://accounts.accesscontrol.windows.net/<ドメイン名またはテナント ID>/FederationMetadata/2007-06/FederationMetadata.xml

	このアプリケーションでは、WS-Federation メタデータの場所を次のように指定します。

		https://accounts.accesscontrol.windows.net/fabrikam.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml 

	メタデータの場所を入力した後、**[次へ]** をクリックします。

5. フェデレーション ユーティリティの次のページで **[証明書チェーン検証を無効にする]** を選択し、**[次へ]** をクリックします。

6. フェデレーション ユーティリティの次のページで **[暗号化しない]** を選択し、**[次へ]** をクリックします。

7. フェデレーション ユーティリティの次のページに、STS が提供するクレームが表示されます。クレームを確認し、**[次へ]** をクリックします。

8. 次に、開発環境で SSL がサポートされるように、インターネット インフォメーション サービス (IIS) を構成します。IIS マネージャーを開くには、**[ファイル名を指定して実行]** プロンプトで「*inetmgr*」と入力します。

9. IIS マネージャーで、左側のメニューの **Sites** フォルダーを展開し、**[既定の Web サイトのホーム]** をクリックします。右側の **[操作]** メニューで、**[バインド]** をクリックします。

10. **[サイト バインド]** ダイアログ ボックスで、**[追加]** をクリックします。**[サイト バインドの追加]** ダイアログ ボックスの **[種類]** ドロップダウン リストを ***[https]*** に変更し、**[SSL 証明書]** で **[IIS Express 開発証明書]** を選択します。**[OK]** をクリックします。

11. IIS マネージャーの左側のメニューの **[アプリケーション プール]** をクリックし、一覧の **[ASP.NET v4.0]** エントリを選択して、右側の **[操作]** メニューの **[詳細設定]** をクリックします。

12. **[詳細設定]** ダイアログ ボックスで "**ユーザー プロファイルの読み込み**" プロパティを **true** に設定します。**[OK]** をクリックします。

13. Visual Studio で、**ソリューション エクスプローラー**からプロジェクトのルートの **Web.config** ファイルを開きます。

14. **Web.config** ファイルで **wsFederation** セクションを探し、サービス プリンシパルの作成時に指定した **$replyUrl** 変数と同じ値を指定して **reply** 属性を追加します。次に例を示します。

		<wsFederation passiveRedirectEnabled="true" issuer="https://accounts.accesscontrol.windows.net/v2/wsfederation" realm="spn: 7829c758-2bef-43df-a685-717089474505" requireHttps="false" reply="https://localhost/OrgIdFederationSample" /> 

15. **httpRuntime** ノードを **system.web** セクション内に追加し、**requestValidationMode** 属性を "**2.0**" に設定します。次に例を示します。

		<system.web>
			<httpRuntime requestValidationMode="2.0" /> 
			...

	**Web.config** ファイルを保存します。

16. これで Web アプリケーションの認証が有効になり、**Index** ページが変更されて、認証されたユーザーのクレームが表示されるようになりました。"**ビュー**" フォルダーの "**ホーム**" フォルダーにある **Index.cshtml** を開きます。

17. 次のコード スニペットを **Index.cshtml** ファイルに追加し、ファイルを保存します。

		<p>
			@if (User.Identity.IsAuthenticated)
			{
			<ul>
				@foreach (string claim in ((Microsoft.IdentityModel.Claims.IClaimsIdentity)this.User.Identity).Claims.Select(c => c.ClaimType + " : " + c.Value))
				{
					<li>@claim</li>
				}
			</ul>
			}
		</p> 

18. **Index.cshtml** ファイルの変更を保存した後、**F5** キーを押してアプリケーションを実行します。
Office 365  の ID プロバイダー ページにリダイレクトされます。このページでディレクトリ テナントの資格情報を使用してログインできます。たとえば、*john.doe@awesomecomputers.onmicrosoft.com* を使用します。

19. 資格情報を使用してサインインすると、Home コントローラーの Index ページにリダイレクトされ、アカウントのクレームが表示されます。このことは、ユーザーが Azure Active Directory によるシングル サインオンを使用して、アプリケーションに対する認証に成功したことを示しています。

<h2><a name="readdata"></a>手順 4. Graph API を使用してディレクトリ データを読み取る</h2>
この手順では、Graph API を使用して、Azure Active Directory テナントに接続し、データを読み取る方法について説明します。Graph API を使い始めるにあたって参考になる ASP.NET アプリケーションとして、[Graph API による書き込みに対応したサンプル アプリケーション]をダウンロードしてください。このアプリケーションには、Graph API に対する認証と要求が簡単になるヘルパー メソッドが含まれています。

また、手順 2. で作成したアプリケーションのサービス プリンシパルにアクセス許可を追加します。これらのアクセス許可を追加するには、AppPrincipalId 値が必要になります。

1. サンプル アプリケーションを目的のフォルダーにダウンロードし、展開します。
2. サンプル コードを使用する前に、追加のアクセス許可をサービス プリンシパルに与える必要があります。これらのアクセス許可により、サービス プリンシパルが Graph API を使用してデータを読み取ることができるようになります。**[スタート]** メニューから **Microsoft Online Services Module for Windows PowerShell** コンソールを実行します。
3. 読み取りアクセス許可をサービス プリンシパルに与えます。そのためには、このプリンシパルにサービス サポート管理者ロールを追加します。サービス プリンシパルへのロールの割り当ての詳細については、「[Azure AD Graph およびロールベースのアクセス制御]」を参照してください。次のコマンドを入力し、Enter キーを押します。

		Add-MsolRoleMember -RoleMemberType "ServicePrincipal" -RoleName "Service Support Administrator" -RoleMemberObjectId $appPrincipal.ObjectId 

4. **$appPrincipal.ObjectId** 変数はサービス プリンシパルの ObjectID です。この ID は次のコマンドを入力して Enter キーを押すことで取得できます。

		Get-MsolServicePrincipal -AppPrincipalId 7829c758-2bef-43df-a685-717089474505 

	<div class="dev-callout"><strong>注</strong><p>この **AppPrincipalId 値は、手順 2. でサービス プリンシパルを作成したときに返された値です。</p></div>

5. サービス プリンシパルにロールを追加した後、Visual Studio を開始し、サンプル アプリケーションのルート **Web.config** ファイルを開きます。

6. **Web.config** で *&lt;configuration&gt;* セクションを見つけたら、*&lt;appSettings&gt;* セクションに移動します。*TenantDomainName*、*AppPrincipalId*、*SymmetricKey* の各キーの値をサービス プリンシパルの値に変更します。次に例を示します。

		<appSettings> 
			...
			<add key="TenantDomainName" value="awesomecomputers.onmicrosoft.com"/> 
			...
			<add key="AppPrincipalId" value="7829c758-2bef-43df-a685-717089474505"/>     
			...
			<add key="SymmetricKey" value="qY+Drf20Zz+A4t2w e3PebCopoCugO76My+JMVsqNBFc=" /> 
			...
		</appSettings>

7. **Web.config** ファイルを保存したら、**F5** キーを押してアプリケーションを実行します。

8. サンプル アプリケーションに、すべてのユーザー、会社管理者などにアクセスするためのメニューが表示されます。リンクのいずれかをクリックすると、テナントに保存されている情報が Graph API を使用して返されます。

<h2><a name="summary"></a>まとめ</h2>
このチュートリアルでは、シングル サインオン Azure Active Directory 機能を使用する 1 つのテナント アプリケーションを作成および構成する方法について説明しました。さらに、Graph API を使用してテナントのディレクトリ データにアクセスしました。サンプル アプリケーションを詳しく調べて独自のアプリケーションで Graph API を活用する方法を理解することをお勧めします。

Graph API の詳細については、[MSDN の関連記事]を参照してください。Azure Active Directory を使用してマルチテナント アプリケーションを作成することもできます。手順については、「[Developing Multi-Tenant Cloud Applications with Azure Active Directory (Azure Active Directory によるマルチテナント クラウド アプリケーションの開発)]」を参照してください。

[はじめに]: #introduction
[手順 1. ASP.NET MVC アプリケーションを作成する]: #createapp
[手順 2. 会社のディレクトリ テナントでアプリケーションをプロビジョニングする]: #provisionapp
[手順 3. WS-Federation を従業員のサインインに使用してアプリケーションを保護する]: #protectapp
[手順 4. Graph API を使用してディレクトリ データを読み取る]: #readdata
[まとめ]: #summary
[Developing Multi-Tenant Cloud Applications with Azure Active Directory (Azure Active Directory によるマルチテナント クラウド アプリケーションの開発)]: http://g.microsoftonline.com/0AX00en/121
[Windows Identity Foundation 3.5 SDK]: http://www.microsoft.com/ja-jp/download/details.aspx?id=4451
[Windows Identity Foundation 1.0 ランタイム]: http://www.microsoft.com/ja-jp/download/details.aspx?id=17331
[Office 365 PowerShell コマンドレット]: http://onlinehelp.microsoft.com/ja-jp/office365-enterprises/ff652560.aspx
[ASP.NET MVC 3]: http://www.microsoft.com/ja-jp/download/details.aspx?id=4211
[WCF Data Services for OData V3]: http://www.microsoft.com/download/en/details.aspx?id=29306
[Graph API による書き込みに対応したサンプル アプリケーション]: http://code.msdn.microsoft.com/Write-Sample-App-for-79e55502
[Azure AD Graph およびロールベースのアクセス制御]: http://msdn.microsoft.com/ja-jp/library/hh974466.aspx
[MSDN の関連記事]: http://msdn.microsoft.com/ja-jp/library/hh974476.aspx

