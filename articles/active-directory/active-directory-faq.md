<properties
	pageTitle="Azure Active Directory に関する FAQ | Microsoft Azure"
	description="Azure Active Directory に関する FAQ"
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/20/2016"
	ms.author="markusvi"/>

# Azure Active Directory に関する FAQ

Azure Active Directory (Azure AD) は、クラウドベースの包括的な ID 管理機能とアクセス管理機能のセットであり、サービスとしての ID (IDaaS) を提供します。Azure AD を IDaaS プロバイダーとして利用すると、対象のコンピューター ネットワーク内で "*だれに何の操作を許可するか*" を管理できます。さらに、Azure AD を使えば、アプリケーションへのシングル サインオン (SSO) を実装できます。SSO により、ユーザーのサインオン エクスペリエンスを大幅に改善できます。詳細については、「[Azure Active Directory とは](active-directory-whatis.md)」をご覧ください。



## Azure と Azure Active Directory へのアクセス

****Q: Azure ポータルで Azure AD が見つからないのはなぜですか (http://portal.azure.com)?**

**A:** Azure AD 管理エクスペリエンスは今も Azure クラシック ポータルで利用できます (`http://manage.windowsazure.com`)。Azure ポータルに統合された新しい管理エクスペリエンスのパブリック プレビューは、9 月までにリリースされる予定です。リリースに関する最新情報については、[Active Directory チームのブログ](https://blogs.technet.microsoft.com/ad/)をご覧ください。


---

****Q: Azure クラシック ポータルで Azure AD にアクセスしようとすると "サブスクリプションが見つかりません" と表示されるのはなぜですか (http://manage.windowsazure.com)?**

**A:** Azure サブスクリプションに対するアクセス許可が必要になります。有料の Office 365 または Azure AD をご利用の場合は、1 回限りのアクティブ化手順を行うために [http://aka.ms/accessAAD](http://aka.ms/accessAAD) に移動してください。これらをご利用でない場合は、完全な [Azure 無料試用版](https://azure.microsoft.com/pricing/free-trial/)または有料サブスクリプションをアクティブ化する必要があります。

---

**Q: Azure AD、Office 365、Azure の関係はどうなっていますか?**

**A:** Microsoft Azure は、オープンで柔軟性の高い、エンタープライズ レベルのクラウド コンピューティング プラットフォームとインフラストラクチャです。Microsoft が管理するデータ センターのグローバル ネットワークを通じて、アプリケーションとサービスを構築、デプロイ、管理できます。たとえば Azure では、Office 365 など、サービスとしてのソフトウェア (SaaS) ソリューションを使用できます。Office 365 は、生産性ソフトウェアと関連するオンライン サービスを提供する、サービス サブスクリプション付きの一連のソフトウェアです。Azure AD はクラウドベースの包括的な ID 管理機能とアクセス管理機能のセットであり、サービスとしての ID (IDaaS) を提供します。この機能により、Office 365 を補完します。Office 365 サブスクリプションをお持ちの場合は、Azure で実行される Azure AD も既に利用できる状態になっています。


---

**Q: どの Azure AD の機能を無料で利用できますか?**

**A:** Azure サブスクリプションのすべての共通機能を無料で利用できます。これらの機能の詳しい一覧については、「[共通機能](active-directory-editions.md/#common-features)」を参照してください。



---

Q: **オンプレミスのディレクトリを Azure AD に接続するにはどうすればよいですか?**

A: オンプレミスのディレクトリを Azure AD に接続するには、**Azure AD Connect** を使用します。詳細については、「[オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)」を参照してください。


---

**Q: オンプレミスのディレクトリとクラウド アプリケーションの間で SSO を設定するにはどうすればよいですか?**

**A:** 必要なのは、オンプレミスのディレクトリと Azure AD の間で SSO を設定することだけです。クラウド アプリケーションを Azure AD で管理している限り、ADFS によるフェデレーションまたはパスワード同期を実装することで、SSO の範囲をオンプレミスの環境に広げることができます。どちらのオプションも **Azure AD Connect** に含まれています。詳細については、「[オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)」を参照してください。
  


---

**Q: Azure AD はオンプレミスのインフラストラクチャの管理に役立ちますか?**

**A:** はい。Azure AD Premium Edition には **Connect Health** が用意されています。Azure AD Connect Health では、オンプレミスの ID インフラストラクチャと同期サービスを監視、分析できます。詳細については、「[クラウド内のオンプレミスの ID インフラストラクチャと同期サービスの監視](active-directory-aadconnect-health.md)」を参照してください。



## パスワード管理

**Q: パスワード同期を使わずに Azure AD パスワード ライトバックを使用することはできますか (パスワード ライトバックを使って Azure AD SSPR を使用したいが、パスワードをクラウドに保存したくない場合)?**

**A:** ライトバックを有効にするために AD パスワードを Azure AD に同期する必要はありません。フェデレーション環境では、Azure AD SSO は、ユーザーの認証のためにオンプレミスのディレクトリを利用します。このシナリオでは、オンプレミスのパスワードを Azure AD で追跡する必要はありません。

---

**Q: パスワードが AD オンプレミスに書き戻されるのに、どれくらいの時間がかかりますか?**

**A:** パスワード ライトバックはリアルタイムで行われます。詳細については、「[パスワード管理の概要](active-directory-passwords-getting-started.md)」を参照してください。


---

**Q: 管理者によって管理されるパスワードでパスワード ライトバックを使用できますか?**

**A:** はい。パスワード ライトバックが有効になっていれば、管理者によって実行されるパスワード操作は、オンプレミスの環境に書き戻されます。パスワード関連の質問に対する回答については、「[パスワード管理に関するよく寄せられる質問 (FAQ)](active-directory-passwords-faq.md)」を参照してください。



## アプリケーション アクセス


**Q: Azure AD とその機能にあらかじめ統合されているアプリケーションの一覧はどこにありますか?**

**A:** Azure AD には、Microsoft、アプリケーション サービス プロバイダー、パートナーが開発した、2,600 個を超える事前統合されたアプリケーションがあります。事前統合されたアプリケーションの完全な一覧については、「[Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/)」をご覧ください。


---

**Q: 必要なアプリケーションが Azure AD Marketplace にない場合はどうすればよいですか?**

**A:** Azure AD Premium なら、お求めのアプリケーションを追加、構成できます。さらに、アプリケーションの機能と必要性に応じて SSO と自動プロビジョニングを構成できます。詳細については、次のリンクを参照してください。

- [Azure Active Directory アプリケーション ギャラリーに含まれていないアプリケーションへのシングル サインオンの構成](active-directory-saas-custom-apps.md)
- [SCIM を使用して、Azure Active Directory からアプリケーションへのユーザーとグループの自動プロビジョニングを有効にする](active-directory-scim-provisioning.md) 



---

**Q: オンプレミスで実行しているアプリケーションを追加することはできますか?**

**A:** Azure AD アプリケーション プロキシを使用すると、SaaS アプリと同じように Azure Active Directory でオンプレミスの Web アプリケーションに簡単にアクセスできます。VPN を導入したり、ネットワーク インフラストラクチャに変更を加えたりする必要はありません。詳細については、「[オンプレミス アプリケーションへの安全なリモート アクセスを実現する方法](active-directory-application-proxy-get-started.md)」を参照してください。


--- 

**Q: 特定のアプリケーションにアクセスするユーザーに対して MFA を要求するにはどうすればよいですか?**

**A:** Azure AD 条件付きアクセスを使用して、各アプリケーションに一意のアクセス ポリシーを割り当てることができます。ポリシーでは、MFA を常に要求するようにしたり、ユーザーがローカル ネットワークに接続されていない場合に要求するようにしたりできます。詳細については、「[Azure Active Directory に接続されている Office 365 とその他のアプリへのアクセスの保護](active-directory-conditional-access.md)」を参照してください。

<!---HONumber=AcomDC_0518_2016-->