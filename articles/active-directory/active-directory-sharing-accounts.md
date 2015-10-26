<properties
	pageTitle="Azure AD を使用したアカウントの共有 | Microsoft Azure"
	description="Azure Active Directory を使用してオンプレミスのアプリケーションおよびコンシューマー クラウド サービス用のアカウントを組織で安全に共有できるようにする方法について説明します。"
	services="active-directory"
	documentationCenter=""
	authors="msStevenPo"
 	manager="stevenpo"
	editor=""/>

 <tags
	ms.service="active-directory"
 	ms.workload="identity"
 	ms.tgt_pltfrm="na"
 	ms.devlang="na"
 	ms.topic="article"
 	ms.date="10/13/2015"  
 	ms.author="stevenpo"/>

# Azure AD とのアカウントの共有

組織では、1 組のユーザー名とパスワードを複数のユーザーで使用することが必要な場合がよくあります。このような状況は、通常、次の 2 つのケースで発生します。

- オンプレミスのアプリケーションであろうと、コンシューマー クラウド サービス (企業のソーシャル メディア アカウントなど) であろうと、ユーザーごとに一意のログインおよびパスワードを必要とするアプリケーションにアクセスする場合。
- マルチユーザー環境を作成する場合。昇格された特権を有し中心となるセットアップ、管理、および回復アクティビティに使用できる単一のローカル アカウントを持つことができます (たとえば、Office 365 のローカル「グローバル管理者」アカウントまたは Salesforce のルート アカウント)。

これまで、このようなアカウントを共有するためには、権限のあるユーザーに資格情報 (ユーザー名/パスワード) を配布するか、または信頼済みの複数のエージェントがアクセスできる共有の場所に当該アカウントを格納するということを行ってきました。

## 従来の共有モデルは何が問題なのか?

従来の共有のモデルには、以下に示すようないくつかの欠点があります。

- 新しいアプリケーションへのアクセスを有効にするには、アクセスを必要としているすべてのユーザーに資格情報を配布する必要があります。
- 各共有アプリケーションでそれぞれ固有の共有資格情報セットを必要とすることがあります。このような場合、ユーザーは複数の資格情報セットを覚えておく必要があります。多くの資格情報を覚えておかなければならない場合、ユーザーはリスクを伴う手段に (たとえば、パスワードを書き留める) 頼るので、リスクは高まります。
- どのユーザーがアプリケーションにアクセスできるのかはっきりしません。
- アプリケーションにアクセスしたユーザーをはっきりさせることができません。
- アプリケーションへのアクセス権を削除する必要がある場合は、資格情報を更新して、アプリケーションへのアクセスを必要としているすべてのユーザーに再配布する必要があります。

## Azure Active Directory はどのように役立つのでしょうか?

Azure AD では、共有のアカウントを使用する上で、上記の欠点を排除した新しい方法を提供します。Azure AD の管理者は、アクセス パネルを使用して、ユーザーがアクセスできるアプリケーションを構成します。管理者は、アプリケーションごとに最適なシングル サインオンの種類を選択します。その中の 1 つであるパスワードベースのシングル サインオンを選択した場合、Azure AD はアプリケーションに対するサインオン プロセス中に一種のブローカーとしての役割を果たします。管理者は、アプリケーションへのアクセス権の付与または取り消しを簡単に行うことができ、また、アカウントへのアクセス権を有するユーザーおよび過去にアプリケーションにアクセスしたユーザーをどの時点でも把握できます。

ユーザーは自分の組織アカウントを使用して一度ログインしたら、それ以降、必ず同じアカウントを使用してデスクトップまたは電子メールにアクセスします。ユーザーは自分が割り当てられているアプリケーションだけを検出し、アクセスすることができます。共有アカウントの場合は、このアプリケーション リストに任意の数の共有資格情報を含めることができます。エンド ユーザーは、使用する可能性があるさまざまなアカウントを覚えることも、書き留めておくことも必要ありません。

共有アカウントは、管理作業の強化、使いやすさの向上だけでなく、セキュリティも強化します。資格情報の使用権限を持つユーザーには、共有パスワードが表示されるのではなく、パスワードを調整された認証フローの一部として使用する権限が与えられます。さらに、一部のパスワード SSO アプリケーションの場合、大規模で複雑なパスワードを使用して Azure AD に定期的にパスワードをロールオーバー (更新) させて、アカウントのセキュリティを強化するオプションもあります。

Azure AD では、アプリケーションに対するあらゆる種類のパスワード シングル サインオンで、エンタープライズ モビリティ スイート (EMS)、Premium、または Basic ライセンス ユーザーを対象にした共有アカウントをサポートします。アプリケーション ギャラリーに事前に統合された何千ものアプリケーションのいずれについてもアカウントを共有することができると共に、[カスタム SSO アプリケーション](active-directory-single-sign-on-newly-acquired-saas-apps.md)を使用して独自のパスワード認証アプリケーションを追加することができます。

Azure AD を使用してアカウントを共有するには、次の操作が必要です。

- アプリケーションを[アプリケーション ギャラリー](https://azure.microsoft.com/JA-JP/marketplace/active-directory/)または[カスタム アプリケーション](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx)に追加する
- パスワード シングル サインオン (SSO) に対応するようにアプリケーションを構成する
- [グループ ベースの割り当て](active-directory-accessmanagement-group-saasapps.md)を使用し、共有資格情報を入力するオプションを選択する
- オプション: Facebook、Twitter、LinkedIn などの一部のアプリケーションでは、[Azure AD 自動パスワード ロール オーバー](http://blogs.technet.com/b/ad/archive/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview.aspx)のオプションを有効にすることができます。

Azure AD を使用した場合、Multi-Factor Authentication (MFA) で共有アカウントの安全性を強化 (詳細については「[Azure AD によるアプリケーションのセキュリティ保護](multi-factor-authentication-get-started.md)」を参照) すると共に、[Azure AD のセルフ サービス](active-directory-accessmanagement-self-service-group-management.md)のグループ管理を使用してアプリケーションへのアクセス権限を有するユーザーを管理する機能を委任することができます。

## 開始するには?

あなたは IT 管理者で、Azure AD をまだ使用したことがない場合:

- [実際に使ってみる](https://azure.microsoft.com/trial/get-started-active-directory/) - 30 日の無料試用版に今すぐサインアップすると、このリンクを使用して、初めてのクラウド ソリューションも 5 分以内にデプロイできます。

アカウントの共有を有効にする Azure AD の機能は、次のとおりです。

- [パスワード シングル サインオン](active-directory-passwords-getting-started.md)
- パスワード シングル サインオン エージェント
- [グループの割り当て](active-directory-accessmanagement-self-service-group-management.md)
- カスタム パスワード アプリケーション
- [アプリケーションの使用状況に関するダッシュ ボード/レポート](active-directory-passwords-get-insights.md)
- エンド ユーザー アクセス ポータル
- [アプリケーション プロキシ](active-directory-application-proxy-get-started.md)
- [Active Directory マーケットプレース](http://azure.microsoft.com/marketplace/active-directory/all/)

関連コンテンツ:

- [条件付きアクセスを使用したアプリケーションの保護](active-directory-conditional-access.md)
- [セルフサービス グループの管理/SSAA](active-directory-accessmanagement-self-service-group-management.md)

<!---HONumber=Oct15_HO3-->