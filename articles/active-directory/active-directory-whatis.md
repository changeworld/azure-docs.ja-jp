<properties 
                pageTitle="Azure Active Directory とは" 
                description="Azure Active Directory を使用すると、既存の内部設置型 ID をクラウドに拡張し、または Azure AD 統合アプリケーションを開発することができます。" 
                services="active-directory" 
                documentationCenter="" 
                authors="markusvi" 
                manager="swadhwa" 
                editor=""/>

<tags 
                ms.service="active-directory" 
                ms.workload="identity" 
                ms.tgt_pltfrm="na" 
                ms.devlang="na" 
                ms.topic="hero-article" 
                ms.date="07/14/2015" 
                ms.author="markusvi"/>


# Azure Active Directory とは





Azure Active Directory (Azure AD) は、マイクロソフトが提供する、マルチテナントに対応したクラウド ベースのディレクトリと ID の管理サービスです。

IT 管理者は、Azure AD により、Office365、Salesforce.com、DropBox、Concur など、[さまざまなクラウド型 SaaS アプリケーション](http://blogs.technet.com/b/ad/archive/2014/09/03/50-saas-apps-now-support-federation-with-azure-ad.aspx)へのシングル サインオン (SSO) アクセスを従業員やビジネス パートナーに提供する使いやすいソリューションを手軽な価格で手に入れることができます。

アプリケーション開発者は、世界中の数多くの組織が使用する卓越した ID 管理ソリューションと Azure AD を高速かつ簡単に統合できるため、アプリケーションの構築に注力できます。

また Azure AD には、Multi-Factor Authentication、デバイスの登録、セルフサービスのパスワード管理、セルフサービスのグループ管理、特権を持つアカウントの管理、ロール ベースのアクセス制御、アプリケーション使用状況の監視、機能豊富な監査とセキュリティの監視、アラートなど、一連の ID 管理機能も用意されています。これらの機能により、クラウド ベース アプリケーションのセキュリティ保護、IT プロセスの効率化、コストの削減を実現し、企業のコンプライアンス目標を確実に満たします。

さらに、わずか [4 回のクリック](http://blogs.technet.com/b/ad/archive/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect.aspx)で Azure AD を既存の Windows Server Active Directory と統合できるため、組織は既存のオンプレミス ID への投資を活用して、クラウド ベースの SaaS アプリケーションへのアクセスを管理できます。

Office365、Azure、Dynamics CRM Online をご利用の方は、既に Azure AD を使用していることを認識していない可能性もあります。実際に、Office365、Azure、Dynamics CRM のテナントは、いずれも既に Azure AD テナントとなっています。必要なときはいつでも、そのテナントを使用して、Azure AD と統合するその他さまざまなクラウド アプリケーションへのアクセスを管理できます。





<center>![Azure AD Connect Stack](./media/active-directory-whatis/Azure_Active_Directory.png) </center>


## Azure AD の信頼性

Azure AD はマルチテナント型で地理的に分散した高可用性のデザインです。つまり Azure AD は、最も重要なビジネス ニーズでも信頼できるツールです。Azure AD は、フェールオーバーが自動化された世界 28 か所のデータ センターから実行されているため、Azure AD の信頼性が高く、データ センターがダウンした場合でも、地理的に分散した 2 か所以上のデータ センターでディレクトリ データのデータが有効で、すぐにアクセスできるという安心感が得られます。



## Azure AD の利点について

組織が Azure AD を使用すると、次のさまざまな方法で、従業員の生産性の向上、IT プロセスの効率化、セキュリティの強化、コストの削減を実現できます。

-	クラウド サービスをすばやく採用し、Azure AD の完全に自動化された SaaS アプリのアクセス管理機能とプロビジョニング サービス機能により、従業員やパートナーが簡単にシングル サインオンできるようにします。
-	従業員は、使いたいデバイスでの作業が必要な場所であれば、どこからでも卓越したクラウド アプリやサービス、セルフサービス機能にアクセスできます。
-	企業のソーシャル メディア アカウントに対する従業員とベンダーのアクセスを簡単かつ安全に管理します。 
-	Azure AD の多要素認証と条件付きアクセスにより、アプリケーションのセキュリティを強化します。
-	一貫したセルフサービスのアプリケーション アクセスの管理を実装することで、経営者は IT コストとオーバーヘッドを削減しながら機動性を強化できます。
-	セキュリティのレポート作成と監視により、アプリケーションの使用状況を監視し、高度な脅威からビジネスを保護します。
-	オンプレミスのアプリケーションへのセキュリティで保護されたモバイル (リモート) アクセスを実現します。






## 開始するには?
-	IT 管理者の場合:
 - [実際に使ってみる](https://azure.microsoft.com/ja-jp/pricing/free-trial/) - 30 日の無料試用版に今すぐサインアップすると、このリンクを使用して、初めてのクラウド ソリューションも 5 分以内にデプロイできます。
 - Azure AD の概要に関するページで、Azure AD テナントをすばやく稼働させるためのヒントとテクニックをお読みください。
-	開発者の場合:
 - Azure Active Directory の「[開発者向けガイド](https://msdn.microsoft.com/library/azure/ff800682.aspx)」を参照してください。<need link>
 - [試用版の開始](https://azure.microsoft.com/ja-jp/pricing/free-trial/) – 30 日の無料試用版に今すぐサインアップして、Azure AD とアプリの統合を開始してください。 



## 詳細情報の入手先

豊富なオンライン リソースにより、Azure AD のすべてについて理解を深めることができます。利用を開始するための優れた記事の一覧を次に示します。


- [Azure AD Connect によるディレクトリのハイブリッド管理を有効にする](active-directory-aadconnect.md)
- [多要素認証とは](multi-factor-authentication.md)
- [Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](active-directory-saas-app-provisioning.md)
- [Password Management のデプロイとユーザー トレーニング](active-directory-passwords-best-practices.md)
- [クラウド上のさまざまな SaaS アプリケーションへのシングル サインオンとアクセスの管理](https://msdn.microsoft.com/library/azure/dn308590.aspx) 
- [オンプレミスのアプリケーションへのリモート アクセスのセキュリティ保護](https://msdn.microsoft.com/library/azure/dn768219.aspx)
- [セルフサービス アクセスの管理](https://msdn.microsoft.com/library/azure/dn641267.aspx) 
- [Cloud App Discovery](https://msdn.microsoft.com/library/azure/mt143581.aspx)
- [デバイスの正常性、ユーザーの場所、ID に基づくアクセス制御](https://msdn.microsoft.com/library/azure/dn906873.aspx)
- [クラウドを利用したオンプレミス ID システムの強化と監視](https://msdn.microsoft.com/library/azure/dn906722.aspx)
- [標準に基づく機能豊富な開発者向けプラットフォーム](https://msdn.microsoft.com/library/azure/ff800682.aspx)

<!---HONumber=July15_HO5-->