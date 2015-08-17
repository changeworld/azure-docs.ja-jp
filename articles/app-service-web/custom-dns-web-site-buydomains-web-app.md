
<properties
	pageTitle="Azure App Service Web Apps でのカスタム ドメイン名の購入方法"
	description="Azure App Service の Web アプリでカスタム ドメイン名を購入する方法について説明します。"
	services="app-service\web"
	documentationCenter=""
	authors="MikeWasson"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/21/2015"
	ms.author="mwasson"/>

# Azure App Service でのカスタム ドメイン名の購入と構成

<div class="dev-center-tutorial-selector sublanding">
  <a href="/documentation/articles/custom-dns-web-site-buydomains-web-app" title="Web アプリ" class="current">Web アプリ用のドメインの購入</a> <a href="/documentation/articles/web-sites-custom-domain-name" title="Web アプリ">外部ドメインでの Web アプリ</a> <a href="/documentation/articles/web-sites-traffic-manager-custom-domain-name/" title="Traffic Manager を使用した Web アプリ">Traffic Manager を使用した Web アプリ</a> <a href="/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a>


</div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../../includes/websites-cloud-services-css-guided-walkthrough.md)]

Web アプリを作成するときに、Azure は azurewebsites.net のサブドメインにそのアプリを割り当てます。たとえば、Web アプリの名前が **contoso** の場合、URL は **contoso.azurewebsites.net** になります。また Azure によって仮想 IP アドレスも割り当てられます。

運用 Web アプリでは、通常、カスタム ドメイン名をユーザーに表示します。この記事では、[App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) を使用してカスタム ドメインを購入して構成する方法について説明します

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]


## 概要

> [AZURE.NOTE]有効なクレジット カードが関連付けられていないサブスクリプションを使用してドメインを購入しないでください。お使いのサブスクリプションが無効になる可能性があります。

Web アプリのドメイン名を持っていない場合は、[Microsoft Azure 管理ポータル](https://portal.azure.com)で簡単に購入できます。購入プロセス中に、WWW とルート ドメインの DNS レコードを自動的に Web アプリにマップできます。Azure Portal 内のドメインの権利を管理することもできます。


次の手順を使用してドメイン名を購入し、Web アプリに割り当てます。

1. ブラウザーで、[Azure の管理ポータル](https://portal.azure.com)を開きます。

2. **[Web Apps]** タブで、Web アプリの名前をクリックし、**[設定]**、**[カスタム ドメインと SSL]** の順に選択します。

	![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

3. **[カスタム ドメインと SSL]** ブレードで **[ドメインを購入]** をクリックします。

	![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

4. **[ドメインを購入]** ブレードで、購入するドメイン名をテキスト ボックスを使用して入力し、Enter キーを押します。推奨される使用可能なドメインがテキスト ボックスの下に表示されます。購入したいドメインを選択します。一度に複数のドメインを購入できます。

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

5. **[連絡先情報]** をクリックし、ドメインの連絡先情報フォームに入力します。

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-3.png)

> [AZURE.NOTE]すべての必須フィールドにできるだけ正確に (特にメール アドレス) 入力することが非常に重要です。"プライバシー保護" のないドメインを購入する場合は、ドメインがアクティブになる前に、メールの確認が求められる可能性があります。連絡先情報に誤ったデータを入力した場合は、ドメインを購入できないことがあります。

6. 次を選択できるようになります。

	a) [Auto renew]: ドメインを毎年自動更新します
	
	b) [Privacy protection]: 無料の購入価格に含まれる、プライバシー保護のためのオプトインです
	
	c) [Assign default hostnames]: 現在の Web アプリに WWW とルート ドメインの既存のホスト名を割り当てます

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.5.png)
  
> [AZURE.NOTE]オプション C は、DNS のバインドとホスト名のバインドを自動的に構成します。これにより、Web アプリは、購入が完了するとすぐにカスタム ドメインを使用してアクセスできます (DNS の伝達が遅れている場合を除く)。Web アプリが Azure Traffic Manager の背後にある場合、A レコードは Traffic Manager で使用できないため、ルート ドメインを割り当てるオプションが表示されません。
>
>Web アプリから購入したドメインやサブドメインは、常に別の Web アプリに割り当てることができます。また、その逆の操作も可能です。詳細については、手順 8. をご覧ください。

	
7. **[ドメインを購入]** ブレードで **[選択]** をクリックすると、**[購入内容の確認]** ブレードに注文情報が表示されます。法律条項に同意して **[購入]** をクリックすると、注文が送信され、**[通知]** で購入プロセスを確認できます。ドメインの購入は完了するまでに数分かかります。 

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-4.png)

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-5.png)

8. ドメインを正常に購入した場合は、ドメインを管理し、Web アプリに割り当てることができます。ドメインの右側にある **[...]** をクリックします。**[購入をキャンセル]** または **[ドメインの管理]** を選択できます。**[ドメインの管理]** をクリックし、**[ドメインの管理]** ブレードで **[サブドメイン]** を Web アプリにバインドします。**[サブドメイン]** を別の Web アプリにバインドする場合は、それぞれの Web アプリのコンテキスト内からこの手順を実行します。ここでは、ドロップ ダウン メニューから Traffic Manager の名前を選択するだけで、ドメインを Traffic Manager エンドポイントに割り当てることができます (Web アプリが TM の背後にある場合)。これにより、ドメインとサブドメインは、Traffic Manager エンドポイントの背後にあるすべての Web アプリに自動的に割り当てられます。 

	![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-6.png)

> [AZURE.NOTE]5 日以内であれば全額返金を受けて "購入をキャンセル" できます。5 日を経過すると "購入をキャンセル" できなくなり、ドメインを "削除" するオプションが表示されます。ドメインを削除すると、返金されずにサブスクリプションが解除され、このドメインは使用可能なドメインになります。

	Once configuration has completed, the custom domain name will be listed in the **Hostname bindings** section of your web app.

この時点でブラウザーにカスタム ドメイン名を入力して、対象の Web アプリに正常にアクセスできることを確認できます。
 

<!---HONumber=August15_HO6-->