<properties
	pageTitle="プロキシ アプリケーションでネイティブ クライアント アプリケーションの発行を有効にする方法 | Microsoft Azure"
	description="ネイティブ クライアント アプリケーションが Azure AD Application Proxy Connector と通信して、オンプレミス アプリケーションに対して安全なリモート アクセスを提供する方法について説明します。"
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/02/2015"
	ms.author="kgremban"/>

# ネイティブ クライアント アプリケーションからプロキシ アプリケーションを操作できるようにする方法
Azure Active Directory アプリケーション プロキシは、SharePoint、Outlook Web Access、カスタムの基幹業務アプリケーションなどのブラウザー アプリケーションを公開するために幅広く使用されています。ネイティブ クライアントから使用される HTTP バックエンド アプリケーションを公開するときにも使用できます。この場合、標準の Authorize HTTP ヘッダーで送信された Azure AD 発行トークンをサポートしている必要があります。


![](./media/active-directory-application-proxy-native-client/richclientflow.png)


このようなアプリケーションを発行するために推奨される方法は、すべての認証ハッスルに対応し、多様なクライアント環境をサポートする Azure AD 認証ライブラリを使用することです。アプリケーション プロキシは、[ネイティブ アプリケーションから Web API のシナリオ](active-directory-authentication-scenarios.md#native-application-to-web-api)に適しています。その手順は次のとおりです。

1. 他のアプリケーションと同様にプロキシ アプリケーションを発行し、ユーザーを割り当てて Premium または Basic ライセンスを付与します。詳細については、「[Azure AD アプリケーション プロキシを使用してアプリケーションを発行する](active-directory-application-proxy-publish.md)」を参照してください。
2. ネイティブ アプリケーションを次のように構成します。
  3. Microsoft Azure 管理ポータルにサインインします。
  4. 左側のメニューの [Active Directory] アイコンをクリックし、目的のディレクトリをクリックします。
  5. 上部のメニューで、[アプリケーション] をクリックします。ディレクトリに追加されているアプリケーションがない場合、このページには [アプリケーションの追加] リンクだけが表示されます。このリンクをクリックします。または、コマンド バーの [追加] をクリックすることもできます。
  4. **[実行する作業を選択してください]** ページで、**[組織で開発中のアプリケーションを追加]** リンクをクリックします。
  5. **[アプリケーション情報の指定]** ページで、アプリケーション名を指定し、電話やコンピューターなど、デバイスにインストールされているアプリケーションを表す **[ネイティブ クライアント アプリケーション]** を選択します。完了したら、ページの右下にある矢印アイコンをクリックします。
  6. **[アプリケーションのプロパティ]** ページに、ネイティブ クライアント アプリケーションの **[リダイレクト URI]** を入力し、ページの右下にあるチェックボックスをオンにします。</br>アプリケーションが追加されると、アプリケーションのクイック スタート ページが表示されます。
8. ディレクトリ内の他のアプリケーションに公開するネイティブ アプリケーションを有効にします。
  9. 上部のメニューから **[アプリケーション]** をクリックし、新しいネイティブ アプリケーションを選択して、**[構成]** をクリックします。
  10. **[他のアプリケーションに対するアクセス許可]** セクションまで下にスクロールします。**[アプリケーションの追加]** をクリックし、ネイティブ アプリケーション アクセス権を付与するプロキシ アプリケーションを選択し、右下のチェックボックスをオンにします。**[委任されたアクセス許可]** ドロップダウン メニューから、新しいアクセス許可を選択します。</br>

![](./media/active-directory-application-proxy-native-client/delegate_native_app.png) </br></br> 4.Active Directory Authentication Library (ADAL) の認証コンテキストのネイティブ アプリケーション コードを編集して、次の情報を含めます。

		// Acquire Access Token from AAD for Proxy Application
		AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<TenantId>");
		AuthenticationResult result = authContext.AcquireToken("< Frontend Url of Proxy App >",
                                                        "< Client Id of the Native app>",
                                                        new Uri("< Redirect Uri of the Native App>"),
                                                        PromptBehavior.Never);

		//Use the Access Token to access the Proxy Application
		HttpClient httpClient = new HttpClient();
		httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
		HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");

変数は次のように置き換えてください。


- **TenantId** は、アプリケーションの **[構成]** ページの URL (“/Directory/” の直後) に表示される GUID です。
- **Frontend URL** は、プロキシ アプリケーションで入力したフロント エンド URL です。プロキシ アプリケーションの **[構成]** ページに表示されます。
- ネイティブ アプリケーションの **Client Id** は、ネイティブ アプリケーションの **[構成]** ページに表示されます。
- **ネイティブ アプリケーションの Redirect URI** は、ネイティブ アプリケーションの **[構成]** ページに表示されます。

![](./media/active-directory-application-proxy-native-client/new_native_app.png) </br> </br>ネイティブ アプリケーション フローの詳細については、「[ネイティブ アプリケーション対 Web API](active-directory-authentication-scenarios.md#native-application-to-web-api)」を参照してください。






## 次の手順
アプリケーション プロキシを使ってできることは他にもたくさんあります。


- [独自のドメイン名でアプリケーションを発行する](active-directory-application-proxy-custom-domains.md)
- [シングル サインオンを有効にする](active-directory-application-proxy-sso-using-kcd.md)
- [要求に対応するアプリケーションを利用する](active-directory-application-proxy-claims-aware-apps.md)
- [条件付きアクセスを有効にする](active-directory-application-proxy-conditional-access.md)


### アプリケーション プロキシに関するその他の情報
- [オンライン ヘルプ](active-directory-application-proxy-enable.md)
- [アプリケーション プロキシに関するブログ](http://blogs.technet.com/b/applicationproxyblog/)
- [Channel 9 ビデオ](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## その他のリソース
* [Azure への組織としてのサインアップ](sign-up-organization.md)
* [Azure ID](fundamentals-identity.md)

<!---HONumber=AcomDC_1125_2015-->