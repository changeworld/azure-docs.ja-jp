<properties 
	pageTitle="" 
	description="Azure Active Directory (Web API プロジェクト) ウィザードの使用開始に関する情報" 
	services="active-directory" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor="tglee"/>
  
<tags 
	ms.service="active-directory" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/06/2015" 
	ms.author="patshea123"/>

# Azure Active Directory の使用 (Web API プロジェクト)

> [AZURE.SELECTOR]
> - [Getting Started](vs-active-directory-webapi-getting-started.md)
> - [What Happened](vs-active-directory-webapi-what-happened.md)

#####コントローラーへのアクセスに対して認証を要求する
 
プロジェクトに含まれるすべてのコントローラーには、**Authorize** 属性が設定されています。この属性により、ユーザーがこれらのコントローラーによって定義された API にアクセスする際に認証が求められます。これらのコントローラーに匿名でアクセスできるようにするには、コントローラーからこの属性を削除します。より細かなレベルでアクセス許可を設定するには、コントローラー クラスではなく、認証を必要とするそれぞれのメソッドに対してこの属性を割り当てます。

[Azure Active Directory の詳細を確認する](http://azure.microsoft.com/services/active-directory/)
 

<!---HONumber=July15_HO3-->