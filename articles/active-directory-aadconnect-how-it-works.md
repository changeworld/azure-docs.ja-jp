<properties 
	pageTitle="Azure AD Connect の動作" 
	description="Azure AD Connect の動作について説明します。" 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="lisatoft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/02/2015" 
	ms.author="billmath"/>

# Azure AD Connect の動作


<div class="dev-center-tutorial-selector sublanding">
<a href="/ja-jp/documentation/articles/active-directory-aadconnect/" title="概要" class="current">概要</a> <a href="/ja-jp/documentation/articles/active-directory-aadconnect-how-it-works/" title="機能">機能</a> <a href="/ja-jp/documentation/articles/active-directory-aadconnect-get-started/" title="使用">使用</a> <a href="/ja-jp/documentation/articles/active-directory-aadconnect-whats-next/" title="次のトピック">次のトピック</a> <a href="/ja-jp/documentation/articles/active-directory-aadconnect-learn-more/" title="詳細">詳細</a>
</div>

Azure Active Directory Connect は Azure Active Directory Connect を構成する 3 つの主要部分から構成されます。これらは、同期サービス、オプションの Active Directory フェデレーション サービス、および Azure AD Connect Health を使用して行う監視です。


<center>![Azure AD Connect スタック](./media/active-directory-aadconnect-how-it-works/AADConnectStack.png) </center>

- 同期 - この部分は、以前 Dirsync と AAD Sync としてリリースされたコンポーネントと機能で構成されます。これは、ユーザーとグループの作成を担当している部分です。オンプレミス環境のユーザーとグループの情報と、クラウド内の情報が一致することも保証します。
- AD FS - これは Azure AD Connect のオプションの部分であり、オンプレミスの AD FS インフラストラクチャを使用して、ハイブリッド環境をセットアップするために使用できます。この部分は、ドメイン参加 SSO、AD のログインのポリシーの適用、スマート カードまたはサード パーティの MFA などを含む複雑なデプロイメントに対応するために、組織で使用できます。SSO の構成に関する詳細については、「[シングル サインオンによる DirSync](https://msdn.microsoft.com/library/azure/dn441213.aspx)」を参照してください。
- 正常性の監視 - AD FS を使用した複雑なデプロイメントでは、Azure AD Connect Health により、フェデレーション サーバーを強力に監視し、このアクティビティを Azure ポータルで一元的に表示できます。詳細については、「[Azure Active Directory Connect Health](https://msdn.microsoft.com/library/azure/dn906722.aspx)」を参照してください。






**その他のリソース**

* [クラウド内のオンプレミスの ID インフラストラクチャの使用](active-directory-aadconnect.md)
* [Azure AD Connect の使用](active-directory-aadconnect-get-started.md)
* [Azure AD Connect の次のトピック](active-directory-aadconnect-whats-next.md)
* [詳細情報](active-directory-aadconnect-learn-more.md)
* [MSDN の Azure AD Connect](https://msdn.microsoft.com/library/azure/dn832695.aspx)

<!---HONumber=58-->