<properties 
	pageTitle="Azure App Service での認証には、Active Directory を使用します" 
	description="Azure アプリ サービスの Web アプリにデプロイされている基幹業務アプリケーションのさまざまな認証オプションと承認オプションについて説明します。" 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="web" 
	ms.date="03/24/2015" 
	ms.author="cephalin"/>

# Azure App Service での認証には、Active Directory を使用します #

[Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) は、内部設置型の環境とパブリック インターネットのどちらからアプリケーションにアクセスしているかにかかわらず、ユーザーのシングル サインオン (SSO) をサポートすることで、企業の基幹業務アプリケーション シナリオを実現します。内部の Active Directory (AD) ユーザーを認証し、適切に承認するために、[Azure Active Directory](http://azure.microsoft.com/services/active-directory/) (AAD)、または Active Directory フェデレーション サービス (AD FS) などの内部設置型のセキュリティ トークン サービス (STS) と統合できます。

## 円滑な認証と承認 ##

ボタンの数回クリックすることにより、Web アプリの認証と承認有効にできます。すべての Azure Web アプリのチェックボックス形式の構成は、基幹業務 Web アプリの基本的なアクセス制御を提供します。これは、すべての Web アプリのコンテンツへのアクセスをユーザーに許可する前に、選択した Azure AD テナントに対して HTTPS および認証を強制することによって実現されます。詳細については、[Web Apps の認証と承認](http://azure.microsoft.com/blog/2014/11/13/azure-websites-authentication-authorization/)に関するブログをご覧ください。

>[AZURE.NOTE]現在、この機能はプレビュー段階にあります。

## 認証と承認の手動での実装 ##

多くのシナリオで、ログインとログアウト ページ、カスタム承認ロジック、マルチ テナント アプリケーションの動作など、アプリケーションの認証と承認の動作をカスタマイズできます。このような場合、機能の柔軟性を高めるために、手動で認証および承認を構成する方がよい場合があります。2 つの主なオプションを次に示します

-	[Azure AD](web-sites-dotnet-lob-application-azure-ad.md) - Azure AD を使用して、Web アプリの認証と承認を実装できます。ID プロバイダーとして Azure AD を使用することには、次の特性があります。
	-	[OAuth 2.0](http://oauth.net/2/)、[OpenID Connect](http://openid.net/connect/)、[SAML 2.0](http://en.wikipedia.org/wiki/SAML_2.0) など、一般的な認証プロトコルをサポートします。サポートされているプロトコルの一覧については、「[Azure Active Directory の認証プロトコル](http://msdn.microsoft.com/library/azure/dn151124.aspx)」をご覧ください。
	-	内部設置型のインフラストラクチャなしで、Azure のみの ID プロバイダーを使用できます。
	-	ディレクトリ同期も、内部設置型 (管理された内部設置型) ADで構成できます。
	-	内部設置型の AD ドメインからディレクトリ同期される Azure AD によって、AD のユーザーがイントラネットおよびインターネットからアクセスするときに、Web アプリへのスムーズな SSO エクスペリエンスが実現します。イントラネットからの AD ユーザーは、統合認証を通じて、自動的に Web アプリにアクセスできます。インターネットからの AD ユーザーは、Windows 資格情報を使用して、Web アプリにログインできます。
	-	[Azure AD でサポートされているすべてのアプリケーション](/marketplace/active-directory/) (Azure、Office 365、Dynamics CRM Online、Microsoft Intune に加え、Microsoft 以外の多数のクラウド アプリケーションなど) に SSO を提供します。 
	-	Azure AD では、[証明書利用者](http://en.wikipedia.org/wiki/Relying_party)アプリケーションの管理を管理者以外のロールに委任しますが、機密性の高いディレクトリ データへのアプリケーション アクセスは、引き続きグローバル管理者が構成する必要があります。
	-	すべての証明書利用者アプリケーションに対して、汎用的な一連の要求タイプを送信します。要求の種類の一覧については、「[サポートされているトークンとクレームの種類](http://msdn.microsoft.com/library/azure/dn195587.aspx)」をご覧ください。要求はカスタマイズできません。
	-	[Azure AD Graph API](http://msdn.microsoft.com/library/azure/hh974476.aspx) により、アプリケーションは Azure AD のディレクトリ データにアクセスできるようになります。
-	[AD FS などの内部設置型のセキュリティ トークン サービス (STS)](../web-sites-dotnet-lob-application-adfs/) - AD FS のような内部設置型の STS を使用して、Web アプリの認証と承認を実装できます。内部設置型の AD FS を使用することには、次の特性があります。
	-	AD FS トポロジは、社内にデプロイする必要があり、コストと管理のオーバーヘッドが伴います。
	-	会社のポリシーによって、AD データを社内に保存することが要求される場合に最適です。
	-	[ AD FS 管理者だけが、証明書利用者の信頼と要求規則](http://technet.microsoft.com/library/dd807108.aspx)を構成できます。
	-	アプリケーションごとに[要求](http://technet.microsoft.com/library/ee913571.aspx)を管理できます。
	-	企業のファイアウォール経由で内部設置型 AD のデータにアクセスするためには、個別のソリューションが必要です。

>[AZURE.NOTE]Azure アカウントにサインアップする前に Azure App Service の使用を開始したい場合は、[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)に関するページをご覧ください。このページでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## 変更内容
* Web サイトから App Service への変更ガイドについては、「[Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)」を参照してください。
* 古いポータルから新しいポータルへの変更ガイドについては、[プレビュー ポータル内の移動に関するリファレンス](http://go.microsoft.com/fwlink/?LinkId=529715)をご覧ください。
 

<!---HONumber=62-->