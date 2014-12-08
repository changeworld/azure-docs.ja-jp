<properties urlDisplayName="What is Azure AD?" pageTitle="Azure の Active Directory とは" metaKeywords="" description="Use Azure Active Directory to extend your existing on-premises identities into the cloud for an improved admin and end user experience while Microsoft keeps Active Directory running in the cloud with high scale, high availability, and integrated disaster recovery. Or, develop Azure AD integrated applications for your organization or for other organizations." metaCanonical="" services="active-directory" documentationCenter="" title="What is Azure Active Directory?" authors="justinha" solutions="" manager="terrylan" editor="" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/03/2014" ms.author="justinha" />





<h1 id="whatisaad">Azure の Active Directory とは</h1>

Azure Active Directory は、クラウドで ID およびアクセス管理機能を提供するサービスです。Active Directory サービスは、顧客が内部設置型 ID 管理のために Windows Server オペレーティング システムを介して利用します。Azure Active Directory (Azure AD) も仕組みはこれとほぼ同じです。つまり、このサービスは、クラウド ベースの ID 管理のために Azure を介して利用されます。[詳細情報](http://msdn.microsoft.com/library/hh967611.aspx)

Active Directory は組織のクラウド ディレクトリなので、そのディレクトリのユーザー、クラウドに保存する情報、その情報の使用者または管理者、およびその情報にアクセスできるようにするアプリケーションまたはサービスを決定します。 

Azure AD を使用するときに、高い拡張性と可用性、および統合障害復旧を備えたクラウドで Active Directory を実行し続けながら、組織の情報のプライバシーおよびセキュリティに関する要件に十分に対応できるようにすることが Microsoft の務めです。

<h3>内部設置型 Active Directory との統合</h3>

Azure AD は組織のスタンドアロン クラウド ディレクトリとして使用できますが、既存の内部設置型 Active Directory と統合することも可能です。統合機能の中には、ディレクトリ同期、シングル サインオンなどが含まれるものがあります。これらの機能は、既存の内部設置型の ID をクラウドに拡張し、管理およびエンド ユーザー エクスペリエンスを強化します。 
 [詳細情報](http://msdn.microsoft.com/library/jj573653)

<h3>アプリケーションとの統合</h3>

アプリケーション開発者は、自身のアプリケーションと Azure AD を統合し、シングル サインオン機能をユーザーに提供できます。これによりエンタープライズ アプリケーションをクラウドでホストし、企業内の資格情報でユーザーを簡単に認証できるようになります。さらに、サービスとしてのソフトウェア (SaaS) プロバイダーが、自身のサービスに対する Azure AD 組織のユーザーの認証を行うとき、その認証がさらに簡単になります。また、開発者は Graph API を使用してディレクトリ データに対してクエリを実行し、ユーザー、グループなどのエンティティを管理することもできます。[詳細情報](http://go.microsoft.com/fwlink/?LinkID=290817&clcid=0x409)

**その他のリソース**

* [Azure への組織としてのサインアップ](/ja-jp/manage/services/identity/organizational-account/)
* [Azure ID](/ja-jp/manage/windows/fundamentals/identity/)
* [MSDN の Azure AD ライブラリ](http://go.microsoft.com/fwlink/?LinkId=293425)
