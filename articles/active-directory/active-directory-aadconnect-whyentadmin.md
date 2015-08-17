<properties 
	pageTitle="エンタープライズ管理者アカウントが必要な理由" 
	description="カスタム設定の説明。" 
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

# Azure AD Connect を設定するときに、AD DS に接続するためのエンタープライズ管理者アカウントが必要な理由

次の表に、Azure AD Connect の設定にエンタープライズ管理者アカウントが必要な理由を示します。

必要な状況 | 説明 
------------- | ------------- |
簡単設定と DirSync のアップグレード | <li>簡単設定では、同期に使用するローカルの Active Directory アカウントを作成し (それ以外の場合は、AD コネクタ アカウントと呼ばれます) 同期とパスワード同期の適切なアクセス許可を割り当てます。</li> <li>Dirsync のアップグレードでは、現在構成されている AD コネクタ アカウントのパスワードをリセットして、このアカウントを使用する新しい Azure AD Connect 同期サービスを構成します。</li>



**その他のリソース**


* [Azure AD Connect アカウントとアクセス許可の詳細](active-directory-aadconnect-account-summary.md)
* [Azure AD Connect のカスタム インストール](active-directory-aadconnect-get-started-custom.md)
* [MSDN の Azure AD Connect](https://msdn.microsoft.com/library/azure/dn832695.aspx) 

<!---HONumber=August15_HO6-->