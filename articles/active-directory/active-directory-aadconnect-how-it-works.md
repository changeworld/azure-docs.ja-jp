<properties 
	pageTitle="Azure AD Connect の動作" 
	description="Azure AD Connect の動作について説明します。" 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# Azure AD Connect の動作

<div class="dev-center-tutorial-selector sublanding">
<a href="../active-directory-aadconnect/" title="概要">概要</a> <a href="../active-directory-aadconnect-how-it-works/" title="動作" class="current">動作</a> <a href="../active-directory-aadconnect-get-started/" title="使用">使用</a> <a href="../active-directory-aadconnect-whats-next/" title="次の手順">次の手順</a> <a href="../active-directory-aadconnect-learn-more/" title="詳細情報">詳細情報</a>
</div>
Azure Active Directory Connect は 3 つの主要な部分で構成されます。この 3 つの部分とは、同期サービス、オプションの Active Directory フェデレーション サービス、および [Azure AD Connect Health](https://msdn.microsoft.com/library/azure/dn906722.aspx) を使用して行う監視です。


<center>![Azure AD Connect スタック](./media/active-directory-aadconnect-how-it-works/AADConnectStack2.png) </center>

- 同期 - この部分は、以前 Dirsync と AAD Sync としてリリースされたコンポーネントと機能で構成されます。これは、ユーザーとグループの作成を担当している部分です。オンプレミス環境のユーザーとグループに関する情報とクラウド内の情報が一致することも保証します。
- AD FS - これは Azure AD Connect のオプションの部分であり、オンプレミスの AD FS インフラストラクチャを使用して、ハイブリッド環境をセットアップするために使用できます。この部分は、ドメイン参加 SSO、AD のログインのポリシーの適用、スマート カードやサード パーティの MFA などを含む複雑なデプロイに対応するために、組織で使用できます。SSO の構成に関する詳細については、「[シングル サインオンを使用した DirSync](https://msdn.microsoft.com/library/azure/dn441213.aspx)」を参照してください。
- 正常性の監視 - AD FS を使用した複雑なデプロイメントでは、Azure AD Connect Health により、フェデレーション サーバーを強力に監視し、このアクティビティを Azure ポータルで一元的に表示できます。詳細については、「[Azure Active Directory Connect Health](https://msdn.microsoft.com/library/azure/dn906722.aspx)」を参照してください。


## Azure AD Connect でサポートされるコンポーネント

次は、前提条件と Azure AD Connect をセットアップしたサーバーでインストールするサポートされたコンポーネントの一覧です。この一覧は、基本的な高速インストール用です。[同期サービスのインストール］ ページで異なる SQL Server の使用を選択した場合は、次に示す SQL Server 2012 のコンポーネントはインストールされません。

- Azure AD Connect の Azure AD コネクタ
- Microsoft SQL Server 2012 のコマンド ライン ユーティリティ
- Microsoft SQL Server 2012 Native Client
- Microsoft SQL Server 2012 Express LocalDB
- Windows PowerShell 用の Azure Active Directory モジュール
- IT プロフェッショナル向け Microsoft Online Services サインイン アシスタント
- Microsoft Visual C++ 2013 再配布パッケージ




 

<!---HONumber=July15_HO3-->