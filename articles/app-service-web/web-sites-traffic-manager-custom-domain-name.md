<properties 
	pageTitle="Traffic Manager を利用する Azure App Service Web アプリのカスタム ドメイン名の構成" 
	description="負荷分散のための Traffic Manager を含む Azure App Service Web アプリのカスタム ドメイン名の使用" 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="cephalin"/>

#Traffic Manager を使用して Azure App Service Web アプリのカスタム ドメイン名を構成する

[AZURE.INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[AZURE.INCLUDE [概要](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

この記事では、負荷分散に Traffic Manager を利用する Azure App Service でカスタム ドメイン名を使用する一般的な手順を示します。

[AZURE.INCLUDE [tmwebsitefooter](../../includes/custom-dns-web-site-traffic-manager-notes.md)]

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

この記事の内容:

-   [DNS レコードについて](#understanding-records)
-   [Web アプリの標準モード用の構成](#bkmk_configsharedmode)
-   [カスタム ドメインの DNS レコードの追加](#bkmk_configurecname)
-   [Web アプリに対する Traffic Manager の有効化](#enabledomain)

<a name="understanding-records"></a>
## DNS レコードについて

[AZURE.INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>
## Web アプリの標準モード用の構成

[AZURE.INCLUDE [modes](../../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>
## カスタム ドメインの DNS レコードの追加

カスタム ドメインを Azure App Service に関連付けるには、ドメイン名を購入したドメイン レジストラーのツールを使用して、新しいエントリをカスタム ドメインの DNS テーブルに追加する必要があります。次の手順を使用して DNS ツールを見つけて利用します。

1. ドメイン レジストラーのアカウントにログオンし、DNS レコードの管理ページを探します。**[ドメイン名]**、**[DNS]**、**[ネーム サーバー管理]** というラベルが付いたサイトのリンクまたは領域を探します。多く場合、このページへのリンクが見つかったら、アカウント情報を表示し、**[My domains]** などのリンクを探します。

4. ドメイン名の管理ページが見つかったら、DNS レコードを編集できるリンクを探します。これは、**[Zone file]**、**[DNS Records]**、**[Advanced]** などの構成リンクとして表示されている場合があります。

	* このページには多くの場合、いくつかのレコードが既に作成されています。たとえば、'**@**' や '*' を  'domain parking' ページに関連付けるエントリが既にあります。**www** のような一般的なサブドメインのレコードが含まれることもあります。
	* このページには **CNAME records** が表示されるか、レコード タイプを選択するためのドロップダウンが表示されます。さらに、**A レコード**や **MX レコード**などの他のレコードが表示されることもあります。場合によっては、CNAME レコードは **Alias Record** などの別の名前で呼ばれています。
	* このページには、**ホスト名**や**ドメイン名**から別のドメイン名に**マッピング**できるフィールドも表示されます。

5. レジストラーによって仕様が異なりますが、一般的には、カスタム ドメイン名 (**contoso.com** など) から、Web アプリに利用される Traffic Manager のドメイン名 (**contoso.trafficmanager.net** など) にマッピングします。 *from* *to*

6. レジストラーで DNS レコードの追加または変更が完了したら、変更を保存します。

<a name="enabledomain"></a>
## Traffic Manager を有効にする

[AZURE.INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

>[AZURE.NOTE] Azure アカウントのサインアップより前に Azure App Service の使用を開始したい場合は、「[アプリ サービスを試す](http://go.microsoft.com/fwlink/?LinkId=523751)」をご覧ください。そこでは、アプリ サービスで有効期間の短いスターター Web アプリをすぐに作成することができます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## 変更内容
* Web サイトからアプリ サービスへの変更ガイドについては、次のものをご覧ください。[Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)
* 古いポータルから新しいポータルへの変更ガイドについては、次のものをご覧ください。[Azure ポータル内の移動に関するリファレンス](http://go.microsoft.com/fwlink/?LinkId=529715)


<!--HONumber=52--> 