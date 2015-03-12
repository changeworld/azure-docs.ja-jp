<properties 
	pageTitle="Windows Azure 多要素認証とは" 
	description="" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="lisatoft"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/17/2014" 
	ms.author="billmath"/>





<h1 id="whatiswamfa">Windows Azure 多要素認証とは</h1>

多要素認証または 2 要素認証は、複数の確認方法の使用を要求することで、ユーザーのサインインとトランザクションにさらなる重要なセキュリティ レイヤーを追加する認証方法です。これらは、次の確認方法のうち 2 つ以上を要求することで機能します。 

* ユーザーが知っているもの (通常はパスワード)
* ユーザーが持っているもの (携帯電話など、簡単には複製できない信頼できるデバイス)
* ユーザー自身 (生体認証)

多要素認証のセキュリティは、レイヤー アプローチによってもたらされます。攻撃者にとって、多要素認証を侵害することはかなり困難です。攻撃者にユーザーのパスワードを知られても、信頼できるデバイスを手に入れなければ役に立ちません。反対に、ユーザーがデバイスを紛失した場合、そのデバイスを見つけた人は、ユーザーのパスワードを知らなければデバイスを使用することができません。
Azure の多要素認証は、モバイル アプリケーション、電話、またはテキスト メッセージを使用したサインインの検証もユーザーに要求する多要素認証です。Azure の Active Directory と同時に使用したり、Azure 多要素認証サーバーによって内部設置型リソースをセキュリティで保護したり、SDK を使用するカスタム アプリケーションおよびディレクトリに使用したりすることができます。 

![Azure Multi-Factor Authentication](./media/multi-factor-authentication/whatismfa.png)

<h3>クラウドの Azure Active Directory のセキュリティ保護</h3>

多要素認証を Azure AD の ID に対して有効にすると、ユーザーが次にサインインするときに追加の確認の設定を求められます。多要素認証を使用すると、Azure、Microsoft Online Services (Office 365、Dynamics CRM Online など)、および Azure AD と統合されているサード パーティのクラウド サービスへのアクセスを追加の設定なしで保護できます。これにより、世界中の多数のユーザーとアプリケーションに対して多要素認証をすばやく有効にすることができます。[詳細情報](http://msdn.microsoft.com/library/azure/dn249466.aspx)

<h3>内部設置型リソースおよび Active Directory でのセキュリティ保護</h3>

IIS や Active Directory などの内部設置型リソースの多要素認証は、Azure 多要素認証サーバーを使用して有効にします。Azure 多要素認証サーバーを使用すると、管理者が IIS 認証と統合して Microsoft IIS Web アプリケーション、RADIUS 認証、LDAP 認証、および Windows 認証をセキュリティで保護することができるようになります。[詳細情報](http://msdn.microsoft.com/library/azure/dn249467.aspx)
<h3>カスタム アプリケーションのセキュリティ保護</h3>

SDK を使用すると、Active Authentication をクラウド サービスに直接統合できます。アプリケーションのサインイン プロセスやトランザクション プロセスに Active Authentication の電話やテキスト メッセージによる確認を組み込んで、アプリケーションの既存のユーザー データベースを活用できます。[詳細情報](http://msdn.microsoft.com/library/azure/dn249464.aspx)

<h3>Office 365 の多要素認証</h3>

Office 365 の多要素認証には、Azure の多要素認証が活かされています。Office 365 アプリケーションに特化し、その管理は Office 365 ポータルから行います。したがって管理者は、多要素認証により Office 365 リソースを保護できるようになります。[詳細情報](http://msdn.microsoft.com/library/azure/dn383636.aspx)

<h3>Azure 管理者用の多要素認証</h3>

すべての Azure 管理者には、Office 365 向けと同じ多要素認証機能が追加費用なしでご利用いただけます。Azure サブスクリプションのすべての管理者アカウントには、この主要な多要素認証機能を有効にすることによって、保護を追加できるようになりました。したがって、Azure ポータルにアクセスして、VM と Web サイトの作成、ストレージの管理、モバイル サービスの使用など、Azure のさまざまなサービスを利用する必要のある管理者は、各自の管理者アカウントに多要素認証を追加することができます。[詳細情報](http://msdn.microsoft.com/library/azure/dn249471.aspx)

<h3>多要素認証の機能の比較</h3>

次に、使用可能な多要素認証のバージョンと、それぞれのバージョンで提供される機能について簡単な概要を示します。これは、どのバージョンの多要素認証が適切であるかを判断するうえで役立ちます。[詳細情報](http://msdn.microsoft.com/library/azure/dn249471.aspx)

![Azure Multi-Factor Authentication Feature Comparison](./media/multi-factor-authentication/mfacomparison2.png)


**その他のリソース**

* [Azure への組織としてのサインアップ](/ja-jp/manage/services/identity/organizational-account/)
* [Azure ID](/ja-jp/manage/windows/fundamentals/identity/)
* [Azure 多要素認証ライブラリ](http://technet.microsoft.com/library/dn249471.aspx)


<!--HONumber=35.2-->

<!--HONumber=46--> 
