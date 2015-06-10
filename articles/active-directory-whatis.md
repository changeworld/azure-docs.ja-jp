<properties
	pageTitle="Azure Active Directory とは何ですか。"
	description="Azure Active Directory を使用すると、既存の内部設置型 ID をクラウドに拡張し、または Azure AD 統合アプリケーションを開発することができます。"
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="terrylan"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/11/2015"
	ms.author="curtand"/>


# Azure Active Directory とは何ですか。


[どのよう動作しますか。](active-directory-works.md)<br> [開始](active-directory-get-started.md)<br> [次のステップ](active-directory-next-steps.md)<br> [詳細](active-directory-learn-map.md)

Azure Active Directory (Azure AD) は、企業が内部設置型のシステムへの投資を拡大して、Active Directory によって常に提供される柔軟性とセキュリティとともにクラウド サービスを活用するための簡単な方法を提供します。 これは、既存の ID インフラストラクチャを拡張することによって、クラウド時代の問題を解決するクラウド プラットフォームであり、何も移行する必要はありません。これまでの投資や内部設置型の機能を生かしたまま、Azure AD を活用してクラウドでの ID とアクセス管理も実現できます。

Azure AD では、アプリケーションやリソースへのアクセスを一元的に制御し、簡単に既存のリソースを追加し (クラウド サービスまたは内部設置型のアプリケーション) 、開発中のアプリケーションを統合することができます。ユーザーは、セルフ サービス機能とすべてのアプリケーションへのシングル サインオン アクセスを利用できます。このため、ユーザーは、各アプリケーションの場所と個別のパスワードを覚えておく必要がありません。企業は、SharePoint サイトに招待する (または、相手方の SharePoint サイトに参加する) ことによって、ビジネス パートナー、サプライヤー、および取引先とクラウドで協力できます。

Azure AD を使用すると、すべてを一元的に管理し、クラウドと内部設置型の両方でその制御を反映できます。管理が重複することはありません。Azure AD は、既存のシステムに適切に統合されます。[詳細情報](active-directory-aadconnect.md)。




> [AZURE.NOTE]Azure Active Directory を使用するには、Azure のアカウントが必要です。アカウントを持っていない場合は、[無料の Azure アカウントにサインアップ](http://azure.microsoft.com/pricing/free-trial/)できます。


## Azure Active Directory で何ができるのでしょうか。

Azure AD は非常に包括的なサービスであるため、組織内のユーザーごとに異なるメリットがあります。

- ビジネスの意思決定者の場合は、ガバナンスの要件を確実に満たしながら、クラウド アプリケーションとモバイル  ワーカーの目的を達成できます。
- サービス プロバイダーの場合は、ID やアクセスのニーズに対応し、自社のサービスを顧客の既存の ID ソリューションに連携させながら、Microsoft Azure や Office 365 の顧客にリーチすることもできます。Azure AD ではバックオフィスのアクセスのニーズにも応えることができます。したがって、自社製、外部委託を問わず、それぞれのユーザーに適したアクセス権を与えることができます。
- IT プロフェッショナル場合は、Azure AD を使用して、「クラウドのスピード」で運用の制御と可視性を高めることができます。 Azure AD では、ユーザーが何を使用しているかを確認し、セルフサービスを提供してユーザー自身に権限を持たせることができます。 <br> <br>

![][1]

##Azure AD について知っておくべき 9 つの重要事項

### 任意の場所からの Active Directory パスワードの管理

Azure Active Directory を使用して、ユーザーが任意の場所、任意のデバイスから、Active Directory または Azure Active Directory のパスワードを管理する権限を与えます。管理者は、パスワードおよび通知のポリシーを管理することができ、発生した時点でパスワードのリセット操作の詳細な監査アクティビティを確認できます。[Azure AD のパスワード管理の詳細情報](http://aka.ms/ssproverview)

### クラウド リソースへのアクセス規則の設定

どのユーザーが、どのような条件下で、クラウド アプリケーションとリソースにアクセスできるかを制御する ルールやポリシーを設定できます。たとえば、多要素認証 (MFA) を要件とし、デバイスや場所に基づいてアクセスを管理できます。[Azure MFA の詳細情報](multi-factor-authentication.md)。

### 任意のアプリケーションへのシングル サインオン

Azure Active Directory は、クラウドと内部設置型アプリケーションに対して、セキュリティで保護されたシングル サインオンを提供します。これには、Microsoft Office 365 と、Salesforce、Workday、DocuSign、ServiceNow、Box などの数千の SaaS アプリケーションが含まれます。[SaaS アプリケーションの詳細情報](http://azure.microsoft.com/marketplace/active-directory/)。

### 任意のデバイスで動作

ユーザーは、既存の業務用の資格情報を使用して、個人用に設定された Web ベースのアクセス パネル、モバイル アプリ、Office 365、またはカスタム社内ポータルからアプリケーションを起動できます。iOS、Mac OS X、Android、または Windows のいずれのデバイスで作業しているかにかかわらず、同じエクスペリエンスが提供されます。

### 外部アクセスのルールの設定

外部ユーザーは、組み込みのアプリケーション プロキシを使用して、ファイアウォールの背後にある内部設置型ネットワークに安全にアクセスできます。多要素認証を含め、設定するルールやポリシーはすべて、アプリケーション プロキシを使用して、クラウド アプリケーションや従来の内部設置型アプリケーションへのアクセスに適用でき、内容を書き換えたり、インターネット上に直接公開する必要はありません。[Azure AD アプリケーション プロキシの詳細情報](https://msdn.microsoft.com/library/azure/dn768219.aspx)。

### ユーザーのアクセスの監視

最後に、Azure AD は組織内で起こっている情報を迅速に提供します。高度なレポート機能や分析によって、ユーザーのアクセスに関する固有の情報を取得できます。たとえば、アプリケーションの検出を使用して、組織内でどのアプリケーションがよく使用されているかを確認できます。[Azure AD のクラウド アプリケーションの検出の詳細情報](https://appdiscovery.azure.com/)。

### 高度なレポート、監査、および分析
Azure AD は、管理者向けに多数の日々の使用状況およびアクセス レポート ([Azure AD レポート](active-directory-view-access-usage-reports.md)) を提供します。また、カスタム レポートとデータの詳細情報が、レポート API ([Azure AD レポート API](active-directory-reporting-api-getting-started.md)) を使用して提供されます。Azure AD の有償エディションでは、特権の必要な操作の監査 ([Azure AD 監査](active-directory-view-access-usage-reports.md)) など、さらに多くのレポートを使用できます。

### すべてのユーザーに対するシンプルかつ安全なエクスペリエンス

例: エンド ユーザーにとっては、自身のプロファイル、アプリケーション、能力を生かしてリソースへのアクセス権を 1 つの場所で、簡単な操作で管理できます。特別なトレーニングは必要ありません。多要素認証、SaaS アプリケーション、ハイブリッド ツール、セルフ サービス機能はすべてすぐに利用できます。

- 管理者には Azure AD 管理ポータルと Windows PowerShell へのアクセス権があり、包括的に管理できます。

- 開発者には、統一された REST ベースの API が用意されており、アプリケーション インターフェイスの公開や使用にも簡単にアクセスできます。

### エディションの選択

Azure AD は次の 3 つのエディションがあります。

- Free エディションは、クラウド ディレクトリ サービスだけを提供します。
- Basic エディションは、SaaS アプリケーションへのアクセスも提供するクラウド ディレクトリ サービスです。
- Premium エディションは、ルールに基づき、セルフ サービスによって管理される包括的なディレクトリ サービス ソリューションです。

ここで説明されている機能を確認するには、[無料評価版の Azure](http://azure.microsoft.com/trial/get-started-active-directory/)をアクティブ化してください。

[Azure AD のエディションの詳細情報](active-directory-editions.md)


## その他のリソース

* [Azure への組織としてのサインアップ](sign-up-organization.md)
* [Azure ID](fundamentals-identity.md)

<!--Image references-->
[1]: ./media/active-directory-whatis/Azure_Active_Directory.png

<!---HONumber=58-->