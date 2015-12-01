<properties
  pageTitle="Azure IoT Suite に関する FAQ | Microsoft Azure"
  description="IoT Suite に関してよく寄せられる質問です。"
  services=""
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="na"
  ms.devlang="na"
  ms.topic="hero-article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="11/17/2015"
  ms.author="araguila"/>
   
# よく寄せられる質問

### サブスクリプションには何個の DocumentDB インスタンスをプロビジョニングできますか?

5 個です。サポート チケットを作成してこの上限を引き上げることができますが、既定では、サブスクリプションごとにプロビジョニングできる DocumentDB インスタンスは 5 個のみです。その結果、サブスクリプションごとにプロビジョニングできるリモート監視構成済みソリューションも最大で 5 個です。

### サブスクリプションでプロビジョニングできる無料の Bing マップ API の数はいくつですか?

2 個です。サブスクリプションで作成できる無料の Bing マップ API は 2 個だけです。リモート監視ソリューションは、既定では無料の Bing マップ API でプロビジョニングされます。その結果、変更なしでサブスクリプションにプロビジョニングできるリモート監視ソリューションは最大 2 個です。

### Azure プレビュー ポータルでのリソース グループの削除と、azureiotsuite.com の構成済みソリューションで削除をクリックすることの違いは何ですか?

- [azureiotsuite.com][lnk-azureiotsuite] で構成済みのソリューションを削除すると、構成済みソリューションの作成時にプロビジョニングしたすべてのリソースが削除されます。リソース グループにリソースを追加してある場合、それらも削除されます。 

- [Azure プレビュー ポータル][lnk-azure-portal]でリソース グループを削除すると、そのリソース グループのリソースだけが削除されます。[Microsoft Azure 管理ポータル][lnk-classic-portal]で構成済みソリューションと関連付けられている Azure Active Directory アプリケーションも削除する必要があります。

### AAD テナントはどうすれば削除できますか?

[Azure AD テナントの削除方法][lnk-delete-aad-tennant]に関する Eric Golpe のブログ投稿を参照してください。


[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx

<!---HONumber=AcomDC_1125_2015-->