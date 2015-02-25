<properties 
	pageTitle="Azure Websites での基幹業務アプリの認証と承認" 
	description="Azure Websites に配置されている基幹業務アプリケーションのさまざまな認証と承認のオプションについて説明します。" 
	services="web-sites" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="web" 
	ms.date="12/23/2014" 
	ms.author="cephalin"/>

# Azure Websites の基幹業務アプリケーションでユーザーを認証および承認する #

[Azure Websites](http://azure.microsoft.com/ja-jp/services/websites/) は、社内環境やパブリック インターネットからのアクセスに関係なくユーザーのシングル サインオン (SSO) をサポートすることで、企業の基幹業務アプリケーション シナリオを実現します。[Azure Active Directory](http://azure.microsoft.com/ja-jp/services/active-directory/) (AAD) または、Active Directory フェデレーション サービス (AD FS) などのオンプレミスのセキュリティ トークン サービス (STS) と統合することで、内部の Active Directory (AD) ユーザーを認証し、適切に承認します。

## 摩擦のない認証と承認 ##

ボタンを数回クリックするだけで、Web サイトの認証と承認を行うことができます。すべての Azure Web サイトにあるチェック ボックス形式の構成が、LOB Web サイトの基本的なアクセス制御を提供します。ユーザーにすべての Web サイト コンテンツへのアクセスを許可する前に、選択した Azure AD テナントに HTTPS と認証を強制することでアクセスを制御します。詳細については、「[Azure Websites Authentication / Authorization (Azure Websites の認証と承認)](http://azure.microsoft.com/blog/2014/11/13/azure-websites-authentication-authorization/)」をご覧ください。

>[AZURE.NOTE] この機能は現在プレビュー段階です。

## 手動による認証と承認の実装 ##

ログイン ページやログアウト ページ、承認ロジック、マルチテナント アプリケーションの動作などのアプリケーションの認証と承認の動作をカスタマイズする必要のある場面は多々あります。このようなときは、その機能の柔軟性を高めるために認証と承認を手動で構成する方がよい場合があります。次の 2 つの方法があります。  

-	[Azure AD](../web-sites-dotnet-lob-application-azure-ad/)  - Azure AD を使用して Web サイトの認証と承認を実装できます。Azure AD を ID プロバイダーとして使用する場合は、次のような特性があります。
	-	一般的な認証プロトコル ([OAuth 2.0](http://oauth.net/2/)、[OpenID Connect](http://openid.net/connect/)、[SAML 2.0](http://en.wikipedia.org/wiki/SAML_2.0) など) をサポートしています。サポートされるプロトコルの完全な一覧については、「[Azure Active Directory Authentication Protocols (Azure Active Directory の認証プロトコル)](http://msdn.microsoft.com/ja-jp/library/azure/dn151124.aspx)」をご覧ください。
	-	オンプレミスのインフラストラクチャがない場合は、Azure 専用の ID プロバイダーを使用できます。
	-	オンプレミス AD (管理されているオンプレミス) とのディレクトリ同期も構成できます。
	-	Azure AD をオンプレミス AD ドメインからディレクトリ同期することで、AD ユーザーがイントラネットやインターネット経由でアクセスする場合、Web サイトにスムーズに SSO を実行できます。イントラネット経由の場合、AD ユーザーは統合認証を通して自動的に Web サイトにアクセスできます。インターネット経由の場合、AD ユーザーは Windows 資格情報を使用して Web サイトにログインできます。
	-	SSO を [Azure AD でサポートされているすべてのアプリケーション](http://azure.microsoft.com/ja-jp/marketplace/active-directory/) (Azure、Office 365、Dynamics CRM Online、Windows InTune、Microsoft 以外の数千のクラウド アプリケーションなど) に提供しています。 
	-	Azure AD は、グローバル管理者が機密性の高いディレクトリ データへのアプリケーションのアクセスを構成する必要がある場合でも、[証明書利用者](http://en.wikipedia.org/wiki/Relying_party)アプリケーションの管理を管理者以外のロールに委任します。
	-	すべての証明書利用者アプリケーションに関する汎用的な一連の要求の種類を送信します。要求の種類の一覧については、「[Supported Token and Claim Types (サポートされているトークンと要求の種類)](http://msdn.microsoft.com/ja-jp/library/azure/dn195587.aspx)」をご覧ください。要求はカスタマイズできません。
	-	[Azure AD Graph API](http://msdn.microsoft.com/ja-jp/library/azure/hh974476.aspx) は、Azure AD のディレクトリ データへのアプリケーションのアクセスを許可します。
-	[オンプレミスのセキュリティ トークン サービス (STS) (AD FS](../web-sites-dotnet-lob-application-adfs/) など) - AD FS などのオンプレミスの STS を使用して Web サイトに認証と承認を実装できます。オンプレミスの AD FS を使用する場合は、次のような特性があります。
	-	コストおよび管理上のオーバーヘッドを伴う AD FS トポロジをオンプレミスに展開する必要があります。
	-	会社のポリシーで、AD データをオンプレミスに保存する必要がある場合に最適です。
	-	AD FS 管理者のみが、[証明書利用者の信頼と要求規則](http://technet.microsoft.com/ja-jp/library/dd807108.aspx)を構成できます。
	-	[要求](http://technet.microsoft.com/ja-jp/library/ee913571.aspx)をアプリケーションごとに管理できます。
	-	会社のファイアウォール経由でオンプレミス AD のデータにアクセスする場合は、個別のソリューションが必要です。


<!--HONumber=42-->
