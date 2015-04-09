<properties 
	pageTitle="Azure App Service でのカスタム ドメイン名の構成 (GoDaddy)" 
	description="GoDaddy から購入したカスタム ドメイン名を Azure Web アプリケーションで使用する方法" 
	services="app-service\web" 
	documentationCenter="" 
	authors="wpickett" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-services-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="wpickett"/>

#Azure App Service でのカスタム ドメイン名の構成 (GoDaddy)

[AZURE.INCLUDE [web-selector](../includes/websites-custom-domain-selector.md)]


[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[AZURE.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

この記事では、[Go Daddy](https://godaddy.com) から購入したカスタム ドメイン名を [App Service の Web アプリケーション](http://go.microsoft.com/fwlink/?LinkId=529714)で使用する手順を示します。

[AZURE.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

この記事の内容:

-   [DNS レコードについて](#understanding-records)
-   [カスタム ドメインの DNS レコードの追加](#bkmk_configurecname)
-   [Web でのドメインの有効化](#enabledomain)

<h2><a name="understanding-records"></a>DNS レコードについて</h2>

[AZURE.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]


<h2><a name="bkmk_configurecname"></a>カスタム ドメインの DNS レコードの追加</h2>

カスタム ドメインを App Service の Web アプリケーションに関連付けるには、GoDaddy のツールを使用して、新しいエントリをカスタム ドメインの DNS テーブルに追加する必要があります。次の手順を使用して GoDaddy.com の DNS ツールを見つけます。

1. GoDaddy.com のアカウントにログオンし、**[My Account]**、**[Manage my domains]** の順に選択します。最後に、Azure Web アプリケーションで使用するドメイン名をドロップ ダウン メニューで選択し、**[Manage DNS]** を選択します。

	![custom domain page for GoDaddy](./media/web-sites-custom-domain-name/godaddy-customdomain.png)

2. **[Domain details]** ページの **[DNS Zone File]** タブまでスクロールします。このセクションを使用して、ドメイン名の DNS レコードを追加したり変更したりします。 

	![DNS Zone File tab](./media/web-sites-custom-domain-name/godaddy-zonetab.png)

	既存のレコードを追加するには、**[Add Record]** を選択します。

	既存のレコードを**編集**するには、レコードの横にあるペンと紙のアイコンを選択します。

	> [AZURE.NOTE] 新しいレコードを追加する前に、GoDaddy では一般的なサブドメイン (Editor では **[Host]**) の DNS レコード (**[email]**、**[files]**、**[mail]** など) が既に作成されていることに注意してください。使用する名前が既に存在する場合は、新しいレコードを作成せずに既存のレコードを変更してください。

4. レコードを追加する場合は、まずレコードの種類を選択する必要があります。

	![select record type](./media/web-sites-custom-domain-name/godaddy-selectrecordtype.png)

	次に、**[Host]** (カスタム ドメインまたはサブドメイン) および **[Points to]** を指定する必要があります。

	![add zone record](./media/web-sites-custom-domain-name/godaddy-addzonerecord.png)

	* **A (ホスト) レコード** を追加するときは、**[Host]** フィールドを、**@** (**contoso.com** などのルート ドメイン名)、* (複数のサブドメインに一致するワイルドカード)、または使用するサブドメイン (**www** など) のいずれかに設定する必要があります。**[Points to]** フィールドを、Azure の Web アプリケーションの IP アドレスに設定する必要があります。
	
		> [AZURE.NOTE] A (ホスト) レコードを使用する場合は、次の構成を持つ CNAME レコードも追加する必要があります。
		> 
		> * **[Host]** 値 **awverify**、**[Points to]** 値 **awverify.&lt;yourwebappname&gt;.azurewebsites.net**。
		> 
		> この CNAME レコードは Azure で使用されて、A レコードで参照されるドメインの所有者が検証されます。

	* **CNAME (エイリアス) レコード** を追加するときは、**[Host]** フィールドを、使用するサブドメインに設定する必要があります。たとえば **www** にします。**[Points to]** フィールドを、Azure の Web アプリケーションの **.azurewebsites.net** ドメイン名に設定する必要があります。たとえば **contoso.azurwebsites.net** にします。


5. レコードの追加または変更が完了したら、**[Finish]** をクリックして変更を保存します。

<h2><a name="enabledomain"></a>Web アプリケーションでのドメイン名の有効化</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

>[AZURE.NOTE] Azure アカウントにサインアップする前に Azure App Service を実際に使ってみるには、[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)にアクセスしてください。App Service に有効期限付きのスターター Web  アプリケーションを無償ですぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## 変更点
* Web サイトから App Service への変更に関するガイド:[Azure App Service と既存の Azure サービスへの影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 以前のポータルから新しいポータルへの変更に関するガイド:[プレビュー ポータルのナビゲートに関するリファレンス](http://go.microsoft.com/fwlink/?LinkId=529715)

<!--HONumber=49-->