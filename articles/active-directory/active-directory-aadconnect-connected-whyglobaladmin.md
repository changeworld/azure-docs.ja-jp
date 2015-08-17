<properties 
	pageTitle="Azure AD Connect のセットアップに Azure AD グローバル管理者アカウントが必要な理由" 
	description="グローバル管理者アカウントが必要な理由についてのカスタム設定の説明です。" 
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

# Azure AD Connect のセットアップに Azure AD グローバル管理者アカウントが必要な理由

次の表に、Azure AD Connect のセットアップに Azure AD グローバル管理者アカウントが必要な理由を示します。

必要な状況 | 説明 
------------- | ------------- |
簡単設定と DirSync のアップグレード | Azure AD ディレクトリの同期を有効にし (必要な場合)、進行中の同期操作で使用する Azure AD アカウント (Azure AD Connector アカウント) を作成します。 
カスタム設定 | Azure AD ディレクトリの同期を有効にし、進行中の同期操作で使用する Azure AD アカウント (Azure AD Connector アカウント) を作成します。AD FS オプションを使用するシングル サインオンの場合は、Azure AD のフェデレーション プロパティを読み取り構成します。



**その他のリソース**


* [Azure AD Connect アカウントとアクセス許可の詳細](active-directory-aadconnect-account-summary.md)
* [Azure AD Connect のカスタム インストール](active-directory-aadconnect-get-started-custom.md)
* [MSDN の Azure AD Connect](https://msdn.microsoft.com/library/azure/dn832695.aspx) 

<!---HONumber=August15_HO6-->