<properties title="Active Directory 認証の使用" pageTitle="" metaKeywords="Azure, Getting Started, Active Directory" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

> [AZURE.SELECTOR]
>
> -   [Getting Started (概要)][Getting Started (概要)]
> -   [変更内容][変更内容]

## Azure Active Directory の使用 (Web API プロジェクト)

##### コントローラーへのアクセスに対して認証を要求する

プロジェクトに含まれるすべてのコントローラーには、**Authorize** 属性が設定されています。この属性により、ユーザーがこれらのコントローラーによって定義された API にアクセスする際に認証が求められます。これらのコントローラーに匿名でアクセスできるようにするには、コントローラーからこの属性を削除します。より細かなレベルでアクセス許可を設定するには、コントローラー クラスではなく、認証を必要とするそれぞれのメソッドに対してこの属性を割り当てます。

[Azure Active Directory の詳細を確認する][Azure Active Directory の詳細を確認する]

  [Getting Started (概要)]: /documentation/articles/vs-active-directory-webapi-getting-started/
  [変更内容]: /documentation/articles/vs-active-directory-webapi-what-happened/
  [Azure Active Directory の詳細を確認する]: http://azure.microsoft.com/services/active-directory/
