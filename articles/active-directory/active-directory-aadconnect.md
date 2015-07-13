<properties 
	pageTitle="Azure AD Connect によるディレクトリのハイブリッド管理を有効にする" 
	description="これは、Azure AD Connect の内容と使用する理由を説明するページです。" 
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
	ms.date="06/22/2015" 
	ms.author="billmath"/>

# Azure AD Connect によるディレクトリのハイブリッド管理を有効にする

<div class="dev-center-tutorial-selector sublanding">
<a href="../active-directory-aadconnect/" title="概要" class="current">概要</a> <a href="../active-directory-aadconnect-how-it-works/" title="動作">動作</a> <a href="../active-directory-aadconnect-get-started/" title="使用">使用</a> <a href="../active-directory-aadconnect-whats-next/" title="次の手順">次の手順</a> <a href="../active-directory-aadconnect-learn-more/" title="詳細情報">詳細情報</a>
</div>


現在、ユーザーはオンプレミスとクラウドの両方でアプリケーションにアクセスできることを望んでいます。ノート PC、スマート フォン、タブレットなど、あらゆるデバイスからアクセスできることが期待されています。この状況に伴い、組織はユーザーがこのようなアプリにアクセスする手段を提供できるようにする必要がありますが、クラウドへの完全移行を必ずしも選択できるわけではありません。

<center>![What is Azure AD Connect](./media/active-directory-aadconnect/arch.png)</center>

Azure Active Directory Connect の導入により、これらのアプリへのアクセス実現とクラウドへの移行がこれまで以上に簡単になりました。Azure AD Connect には、次の利点があります。

- ユーザーは、共通 ID を使用してクラウドとオンプレミスの両方にサインオンできます。ユーザーは複数のパスワードまたはアカウントを覚える必要がなく、管理者は複数のアカウント使用に伴う追加のオーバーヘッドを心配する必要がありません。
- Azure Active Directory によるオンプレミスのディレクトリの接続は、1 つのツールで簡単に行うことができます。インストールすると、ウィザードを通じて、同期サービス、パスワードの同期または AD FS、Azure AD PowerShell モジュールなどの必須コンポーネントを含む、ディレクトリ統合の開始に必要なすべてのコンポーネントをデプロイして構成します。



<center>![What is Azure AD Connect](./media/active-directory-aadconnect/azuread.png)</center>




## Azure AD Connect を使用する理由 

オンプレミスのディレクトリと Azure AD を統合すると、クラウドとオンプレミス両方のリソースにアクセスするための共通の ID が提供されるため、ユーザーの生産性が向上します。この統合によって、ユーザーや組織には次の利点があります。
	
* 組織は、Windows Server Active Directory を利用した後に Azure Active Directory に接続して、オンプレミスとクラウドベースのサービス全体で共通するハイブリッド ID をユーザーに提供できます。 
* 管理者は、アプリケーション リソース、デバイスおよびユーザーの ID、ネットワークの場所、多要素認証に基づき、条件付きアクセスを提供できます。
* ユーザーは、共通 ID を Azure AD、Office 365、Intune、SaaS アプリ、サードパーティ アプリケーションのアカウントで利用できます。  
* 開発者は、共通 ID モデルを利用したアプリケーションを構築し、アプリケーションをオンプレミスの Active Directory またはクラウドベース アプリケーション向け Azure に統合できます。

Azure AD Connect を使用すると、このような統合が簡単になり、オンプレミスとクラウドの ID インフラストラクチャの管理が簡素化されます。



----------------------------------------------------------------------------------------------------------
## Azure AD Connect のダウンロード

Azure AD Connect の使用を開始するには、[Azure AD Connect のダウンロード](http://go.microsoft.com/fwlink/?LinkId=615771) ページから最新バージョンをダウンロードすることができます。

----------------------------------------------------------------------------------------------------------








 

<!---HONumber=62-->