<properties
	pageTitle="Azure AD アプリケーション プロキシで発行されたアプリケーションの条件付きアクセス"
	description="Azure AD アプリケーション プロキシを使用してリモート アクセスを発行するアプリケーションの条件付きアクセスを設定する方法について説明します。"
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="StevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/07/2016"
	ms.author="kgremban"/>

# 条件付きアクセスの使用
> [AZURE.NOTE]アプリケーション プロキシは、Azure Active Directory の Premium または Basic エディションにアップグレードしている場合にのみ利用できる機能です。詳細については、「[Azure Active Directory のエディション](active-directory-editions.md)」をご覧ください。

これで、アプリケーション プロキシを使用して発行されたアプリケーションへのユーザーとグループのアクセスに条件付きアクセスを許可するアクセス規則を有効にできます。これを使用すると、次のことができます。

- アプリケーションごとに Mutli-Factor Authentication を要求する
- ユーザーが社外からアクセスする場合にのみ Mutli-Factor Authentication を要求する
- ユーザーが社外からアプリケーションにアクセスするのをブロックする

これらの規則は、すべてのユーザーとグループに適用したり、特定のユーザーとグループにのみ適用したりすることができます。既定では、この規則は、アプリケーションへのアクセスを持つすべてのユーザーに適用されます。ただし、規則の適用は指定したセキュリティ グループのメンバーであるユーザーのみに制限することもできます。

アクセス規則は、ユーザーが OAuth 2.0、OpenID Connect、SAML、または WS-Federation を使用するフェデレーション アプリケーションにアクセスした場合に評価されます。さらに、アクセス規則は、アクセス トークンを取得するために更新トークンが使用された場合に、OAuth 2.0 と OpenID Connect によって評価されます。

## 条件付きアクセスの前提条件

- Azure Active Directory Premium のサブスクリプション
- フェデレーションまたは管理対象 Azure Active Directory テナント
- フェデレーション テナントでは、Multi-Factor Authentication (MFA) を有効にする必要があります。

![アクセス規則の構成 - 多要素認証が必要です](./media/active-directory-application-proxy-conditional-access/application-proxy-conditional-access.png)

## アプリケーションごとの Multi-Factor Authentication の構成
1. Azure クラシック ポータルの管理者としてサインインします。
2. Active Directory に移動し、アプリケーション プロキシを有効にするディレクトリを選択します。
3. **[アプリケーション]** をクリックし、下へスクロールして **[アクセス規則]** セクションを表示します。アクセス規則セクションは、フェデレーション認証を使用するアプリケーション プロキシを使用して発行されたアプリケーションに対してのみ表示されます。
4. **[アクセス規則を有効にする]** を選択して、**[オン]** にすると、規則が有効になります。
5. 規則を適用するユーザーとグループを指定します。**[グループの追加]** を使用して、アクセス規則を適用する 1 つ以上のグループを選択します。このダイアログ ボックスを使用して、選択したグループを削除することもできます。グループに適用する規則が選択されている場合は、指定したセキュリティ グループのいずれかに属しているユーザーのみにアクセス規則が適用されます。  

  - 規則からセキュリティ グループを明示的に除外するには、**[除外]** をオンにし、1 つ以上のグループを指定します。除外一覧内のグループのメンバーであるユーザーに、Multi-Factor Authentication を実行する必要はありません。  

  - ユーザーごとの Multi-Factor Authentication を使用してユーザーが構成されている場合は、この設定がアプリケーションの Multi-Factor Authentication 規則に優先します。これは、たとえ、それらのユーザーがアプリケーションの Multi-Factor Authentication 規則から除外されていたとしても、Multi-Factor Authentication を実行するには、ユーザーごとの Multi-Factor Authentication で構成されたユーザーが必要になることを意味します。[Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md) とユーザーごとの設定に関する詳細を参照してください。

6. 設定するアクセス規則を選択します。
	- **Multi-Factor Authentication が必要です**: アクセス規則が適用されるユーザーは、規則が適用されるアプリケーションにアクセスする前に Multi-Factor Authentication を完了する必要があります。
	- **会社の外にいる場合、Multi-Factor Authentication が必要です**: 信頼できる IP アドレスからアプリケーションにアクセスしようとしているユーザーに、Multi-Factor Authentication を実行する必要はありません。Multi-Factor Authentication の設定ページでは、信頼できる IP アドレスの範囲を構成できます。
	- **社外からのアクセスをブロック**: 企業ネットワークの外部からアプリケーションにアクセスしようとするユーザーは、アプリケーションにアクセスできません。


## フェデレーション サービスの MFA の構成
フェデレーション テナントでは、Multi-Factor Authentication (MFA) が、Azure Active Directory またはオンプレミスの AD FS サーバーによって実行される場合があります。既定では、MFA は Azure Active Directory によってホストされているページで実行されます。オンプレミスの MFA を構成するには、Windows PowerShell を実行し、–SupportsMFA プロパティを使用して、Azure AD モジュールを設定します。

次の例では、contoso.com テナントで [Set-MsolDomainFederationSettings コマンドレット](https://msdn.microsoft.com/library/azure/dn194088.aspx)を使用して、オンプレミスの MFA を有効にする方法を示します。`Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true `

このフラグの設定に加えて、Multi-Factor Authentication が実行されるように、フェデレーション テナントの AD FS インスタンスを構成する必要があります。[オンプレミスの Microsoft Azure Multi-Factor Authentication のデプロイ](../multi-factor-authentication/multi-factor-authentication-get-started-server.md)の手順に従ってください。


## 関連項目
アプリケーション プロキシを使ってできることは他にもたくさんあります。

- [アプリケーション プロキシを使用してアプリケーションを発行する](active-directory-application-proxy-publish.md)
- [独自のドメイン名でアプリケーションを発行する](active-directory-application-proxy-custom-domains.md)
- [シングル サインオンを有効にする](active-directory-application-proxy-sso-using-kcd.md)
- [要求に対応するアプリケーションを利用する](active-directory-application-proxy-claims-aware-apps.md)
- [アプリケーション プロキシで発生した問題のトラブルシューティングを行う](active-directory-application-proxy-troubleshoot.md)

## アプリケーション プロキシに関するその他の情報
- [オンライン ヘルプ](active-directory-application-proxy-enable.md)
- [アプリケーション プロキシに関するブログ](http://blogs.technet.com/b/applicationproxyblog/)
- [Channel 9 ビデオ](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## その他のリソース

* [Azure への組織としてのサインアップ](sign-up-organization.md)
* [Azure ID](fundamentals-identity.md)

<!---HONumber=AcomDC_0114_2016-->