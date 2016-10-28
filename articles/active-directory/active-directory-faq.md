<properties
	pageTitle="Azure Active Directory に関する FAQ | Microsoft Azure"
	description="Azure と Azure Active Directory へのアクセス、パスワード管理、アプリケーション アクセスに関する質問にお答えする Azure Active Directory の FAQ。"
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/16/2016"
	ms.author="markusvi"/>

# Azure Active Directory に関する FAQ

Azure Active Directory は、包括的なサービスとしての ID (IDaaS) ソリューションです。その範囲は ID、アクセス管理、セキュリティのあらゆる面に及びます。


詳細については、「[Azure Active Directory とは](active-directory-whatis.md)」をご覧ください。



## Azure と Azure Active Directory へのアクセス


**Q: Azure クラシック ポータルで Azure AD にアクセスしようとすると "サブスクリプションが見つかりません" と表示されるのはなぜですか (https://manage.windowsazure.com)?**

**A:** Azure クラシック ポータルにアクセスするには、Azure サブスクリプションに対するアクセス許可が各ユーザーに付与されている必要があります。有料の Office 365 または Azure AD をご利用の場合は、1 回限りのアクティブ化手順を行うために [http://aka.ms/accessAAD](http://aka.ms/accessAAD) に移動してください。これらをご利用でない場合は、完全な [Azure 無料試用版](https://azure.microsoft.com/pricing/free-trial/)または有料サブスクリプションをアクティブ化する必要があります。

詳細については、次のリンクを参照してください。

- [Azure サブスクリプションを Azure Active Directory に関連付ける方法](active-directory-how-subscriptions-associated-directory.md)

- [Azure での Office 365 サブスクリプションのディレクトリの管理](active-directory-manage-o365-subscription.md)

---

**Q: Azure AD、Office 365、Azure の関係はどうなっていますか?**

**A:** Azure Active Directory を使用すると、すべての Microsoft オンライン サービスに共通の ID とアクセス機能が提供されます。Office 365、Microsoft Azure、Intune などのサービスをご利用の場合は、Azure AD を既に使用しており、これらのサービスすべてでサインオンとアクセス管理が有効になっています。

実際に、Microsoft オンライン サービスが有効になっているすべてのユーザーは、1 つ以上の Azure AD インスタンスでユーザー アカウントとして定義されます。クラウド アプリケーション アクセスなど、無料の Azure AD 機能をこれらのアカウントで有効にすることができます。
 
さらに、Azure AD 有料サービス (Azure AD Basic、Premium、EMS など) は、エンタープライズ規模の包括的な管理ソリューションとセキュリティ ソリューションによって、Office 365 や Microsoft Azure などの他のオンライン サービスを補完します。


---



## ハイブリッド Azure AD の使用


**Q: オンプレミスのディレクトリを Azure AD に接続するにはどうすればよいですか?**

**A:** オンプレミスのディレクトリを Azure AD に接続するには、**Azure AD Connect** を使用します。

詳細については、「[オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)」を参照してください。


---

**Q: オンプレミスのディレクトリとクラウド アプリケーションの間で SSO を設定するにはどうすればよいですか?**

**A:** 必要なのは、オンプレミスのディレクトリと Azure AD の間で SSO を設定することだけです。Azure AD を介してクラウド アプリケーションにアクセスしている限り、自動的にこのサービスによって、オンプレミスの資格情報を使ったユーザー認証が正しく行われます。

オンプレミスからの SSO は、ADFS などのフェデレーション ソリューションを使用したり、パスワード ハッシュ同期を構成したりすることによって簡単に実装できます。どちらのオプションも Azure AD Connect 構成ウィザードを使って簡単にデプロイできます。
  

詳細については、「[オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)」を参照してください。
  

---

**Q: Azure Active Directory には、所属する組織のユーザーのためのセルフサービス ポータルが用意されていますか?**

**A:** はい。Azure Active Directory には、ユーザーによるセルフサービスとアプリケーション アクセス用に [Azure AD アクセス パネル](http://myapps.microsoft.com)が用意されています。Office 365 を利用している方は、これと同じ機能の多くを Office 365 ポータルで使用できます。

詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」を参照してください。



---

**Q: Azure AD はオンプレミスのインフラストラクチャの管理に役立ちますか?**

**A:** はい。Azure AD Premium Edition には **Connect Health** が用意されています。Azure AD Connect Health では、オンプレミスの ID インフラストラクチャと同期サービスを監視、分析できます。

詳細については、「[クラウド内のオンプレミスの ID インフラストラクチャと同期サービスの監視](active-directory-aadconnect-health.md)」を参照してください。

---

## パスワード管理

**Q: パスワード同期を使わずに Azure AD パスワード ライトバックを使用することはできますか (パスワード ライトバックを使って Azure AD SSPR を使用したいが、パスワードをクラウドに保存したくない場合)?**

**A:** ライトバックを有効にするために AD パスワードを Azure AD に同期する必要はありません。フェデレーション環境では、Azure AD SSO は、ユーザーの認証のためにオンプレミスのディレクトリを利用します。このシナリオでは、オンプレミスのパスワードを Azure AD で追跡する必要はありません。

---

**Q: パスワードが AD オンプレミスに書き戻されるのに、どれくらいの時間がかかりますか?**

**A:** パスワード ライトバックはリアルタイムで行われます。

詳細については、「[パスワード管理の概要](active-directory-passwords-getting-started.md)」を参照してください。


---

**Q: 管理者によって管理されるパスワードでパスワード ライトバックを使用できますか?**

**A:** はい。パスワード ライトバックが有効になっていれば、管理者によって実行されるパスワード操作は、オンプレミスの環境に書き戻されます。

パスワード関連の質問に対する回答については、「[パスワード管理に関するよく寄せられる質問 (FAQ)](active-directory-passwords-faq.md)」を参照してください。

---

## アプリケーション アクセス


**Q: Azure AD とその機能にあらかじめ統合されているアプリケーションの一覧はどこにありますか?**

**A:** Azure AD には、Microsoft、アプリケーション サービス プロバイダー、パートナーが開発した、2,600 個を超える事前統合されたアプリケーションがあります。すべての事前統合されたアプリケーションで SSO がサポートされています。SSO を使用すると、対象のアプリへのアクセスに、所属する組織の資格情報を使うことができます。また、一部のアプリケーションでは、プロビジョニングとプロビジョニング解除の自動化がサポートされています。

事前統合されたアプリケーションの完全な一覧については、「[Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/)」を参照してください。


---

**Q: 必要なアプリケーションが Azure AD Marketplace にない場合はどうすればよいですか?**

**A:** Azure AD Premium なら、お求めのアプリケーションを追加、構成できます。アプリケーションの機能と必要性に応じて SSO と自動プロビジョニングを構成できます。

詳細については、次のリンクを参照してください。

- [Azure Active Directory アプリケーション ギャラリーに含まれていないアプリケーションへのシングル サインオンの構成](active-directory-saas-custom-apps.md)
- [SCIM を使用して、Azure Active Directory からアプリケーションへのユーザーとグループの自動プロビジョニングを有効にする](active-directory-scim-provisioning.md)


---

**Q: ユーザーが Azure Active Directory を使用してアプリケーションにサインインする場合、どのような方法がありますか?**
 
**A:** Azure Active Directory では、ユーザーがアプリケーションを表示してアクセスする方法は複数あります。たとえば、次の方法があります。

- Azure AD アクセス パネル

- Office 365 アプリケーション起動プログラム

- フェデレーション アプリへの直接サインオン

- フェデレーション アプリ、パスワードベースのアプリ、または既存のアプリへのディープ リンク

詳細については、「[Azure AD 統合アプリケーションをユーザーにデプロイする](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)」を参照してください。


---

**Q: Azure Active Directory でアプリケーションに対する認証とシングル サインオンを有効にする方法には、どのようなものがありますか?**
 
**A:** Azure Active Directory では、SAML 2.0、OpenID Connect、OAuth 2.0、WS-Federation など、認証と承認に関する標準化されたプロトコルが多数サポートされています。Azure AD では、フォーム ベース認証しかサポートしていないアプリのために、パスワード保管と自動サインインの機能もサポートされています。

詳細については、次を参照してください。

- [Azure AD の認証シナリオ](active-directory-authentication-scenarios.md)

- [Active Directory の認証プロトコル](https://msdn.microsoft.com/library/azure/dn151124.aspx)

- [Azure Active Directory によるシングル サインオンのしくみ](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)


---

**Q: オンプレミスで実行しているアプリケーションを追加することはできますか?**

**A:** Azure AD アプリケーション プロキシを使用すれば、選択したオンプレミスの Web アプリケーションに簡単かつ安全にアクセスできます。これらのアプリケーションには、Azure Active Directory で SaaS アプリにアクセスしているのと同じ方法でアクセスできます。VPN を使ったり、ネットワーク インフラストラクチャを変更したりする必要はありません。

詳細については、「[オンプレミス アプリケーションへの安全なリモート アクセスを実現する方法](active-directory-application-proxy-get-started.md)」を参照してください。


--- 

**Q: 特定のアプリケーションにアクセスするユーザーに対して MFA を要求するにはどうすればよいですか?**

**A:** Azure AD 条件付きアクセスを使用して、各アプリケーションに一意のアクセス ポリシーを割り当てることができます。ポリシーでは、MFA を常に要求するようにしたり、ユーザーがローカル ネットワークに接続されていない場合に要求するようにしたりできます。

詳細については、「[Azure Active Directory に接続されている Office 365 とその他のアプリへのアクセスの保護](active-directory-conditional-access.md)」を参照してください。


---

**Q: SaaS アプリへのユーザー プロビジョニングの自動化とはなんですか?**

**A:** Azure Active Directory を使用すると、多くの一般的なクラウド (SaaS) アプリケーションでユーザー ID の作成、保守、削除を自動化できます。

詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](active-directory-saas-app-provisioning.md)」を参照してください。

---

<!---HONumber=AcomDC_0817_2016-->