<properties
	pageTitle="Azure App Service Web Apps でのカスタム ドメイン名の購入方法"
	description="Azure App Service の Web アプリでカスタム ドメイン名を購入する方法について説明します。"
	services="app-service\web"
	documentationCenter=""
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2016"
	ms.author="robmcm"/>

# Azure App Service でのカスタム ドメイン名の購入と構成

[AZURE.INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

Web アプリを作成するときに、Azure は azurewebsites.net のサブドメインにそのアプリを割り当てます。たとえば、Web アプリの名前が **contoso** の場合、URL は **contoso.azurewebsites.net** になります。また Azure によって仮想 IP アドレスも割り当てられます。

運用 Web アプリでは、通常、カスタム ドメイン名をユーザーに表示します。この記事では、[App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) を使用してカスタム ドメインを購入して構成する方法について説明します

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]


## Overview

Web アプリのドメイン名を持っていない場合は、[Azure ポータル](https://portal.azure.com/)で簡単に購入できます。購入プロセス中に、WWW とルート ドメインの DNS レコードを自動的に Web アプリにマップできます。Azure Portal 内のドメインの権利を管理することもできます。


次の手順を使用してドメイン名を購入し、Web アプリに割り当てます。

1. ブラウザーで、[Azure ポータル](https://portal.azure.com/)を開きます。

2. **[Web Apps]** タブで、Web アプリの名前をクリックし、**[設定]**、**[カスタム ドメイン]** の順に選択します。

	![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

3. **[カスタム ドメイン]** ブレードで **[ドメインの購入]** をクリックします。

	![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

4. **[ドメインを購入]** ブレードで、購入するドメイン名をテキスト ボックスを使用して入力し、Enter キーを押します。推奨される使用可能なドメインがテキスト ボックスの下に表示されます。購入したいドメインを選択します。一度に複数のドメインを購入できます。

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

5. **[連絡先情報]** をクリックし、ドメインの連絡先情報フォームに入力します。

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-3.png)

    > [AZURE.NOTE] It is very important that you fill out all required fields with as much accuracy as possible, especially the email address. In case of purchasing the domain without "Privacy protection", you might be asked to verify your email before the domain becomes active. In some cases, incorrect data for contact information will result in failure to purchase domains. 

6. 次を選択できるようになります。

	a) [Auto renew]: ドメインを毎年自動更新します
	
	b) [Privacy protection]: 無料の購入価格に含まれる、プライバシー保護のためのオプトインです (レジストリがプライバシーをサポートしていない TLD を除きます。たとえば、.co.in や .co.uk などです)
	
	c) [Assign default hostnames]: 現在の Web アプリに WWW とルート ドメインの既存のホスト名を割り当てます

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.5.png)
  
    > [AZURE.NOTE] Option C configures DNS bindings and Hostname bindings automatically for you.  This way, your Web App can be accessed using custom domain as soon as the purchase is complete (baring DNS propagation delays in few cases). In case, your Web App is behind Azure Traffic Manager, you will not see an option to assign root domain, as A-Records do not work with the Traffic Manager. 
    You can always assign the domains/sub-domains purchased through one Web App to another Web App and vice-versa. See step 8 for more details. 
	
7. **[ドメインを購入]** ブレードで **[選択]** をクリックすると、**[購入内容の確認]** ブレードに注文情報が表示されます。法律条項に同意して **[購入]** をクリックすると、注文が送信され、**[通知]** で購入プロセスを確認できます。ドメインの購入は完了するまでに数分かかります。

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-4.png)

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-5.png)

8. ドメインを正常に購入した場合は、ドメインを管理し、Web アプリに割り当てることができます。ドメインの右側にある **[...]** をクリックします。**[購入をキャンセル]** または **[ドメインの管理]** を選択できます。**[ドメインの管理]** をクリックし、**[ドメインの管理]** ブレードで **[サブドメイン]** を Web アプリにバインドします。**[サブドメイン]** を別の Web アプリにバインドする場合は、それぞれの Web App のコンテキスト内からこの手順を実行します。ここでは、ドロップ ダウン メニューから Traffic Manager の名前を選択するだけで、ドメインを Traffic Manager エンドポイントに割り当てることができます (Web App が TM の背後にある場合)。これにより、ドメインとサブドメインは、Traffic Manager エンドポイントの背後にあるすべての Web Apps に自動的に割り当てられます。

	![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-6.png)

    > [AZURE.NOTE] 5 日以内であれば全額返金を受けて "購入をキャンセル" できます。5 日を経過すると "購入をキャンセル" できなくなり、ドメインを "削除" するオプションが表示されます。ドメインを削除すると、ドメインがサブスクリプションから解除されますが返金されません。また、このドメインは使用可能なドメインになります。

構成が完了すると、カスタム ドメイン名が Web アプリの **[ホスト名のバインド]** セクションに表示されます。

この時点でブラウザーにカスタム ドメイン名を入力して、対象の Web アプリに正常にアクセスできることを確認できます。
 
## 購入したカスタム ドメインはどうなるか

**[カスタム ドメインおよび SSL]** ブレードで購入したカスタム ドメインは Azure サブスクリプションに関連付けられます。Azure リソースとしては、このカスタム ドメインは、最初にドメインを購入した App Service アプリとは別であり、独立しています。これは、次のことを意味します。

- Azure ポータル内では、購入したカスタム ドメインを、最初にカスタム ドメインを購入したときのアプリだけでなく、複数の App Service アプリに使用できます。
- Azure サブスクリプションで購入したすべてのカスタム ドメインは、そのサブスクリプションの "*任意の*" App Service アプリの **[カスタム ドメインおよび SSL]** ブレードで管理できます。
- そのカスタム ドメイン内のサブドメインに、同じ Azure サブスクリプションからあらゆる App Service アプリを割り当てることができます。
- App Service アプリを削除する場合、そのアプリに関連付けられているカスタム ドメインを他のアプリで引き続き使用するのであれば、カスタム ドメインを削除しないことを選択できます。

## 購入したカスタム ドメインが表示されない場合

**[カスタム ドメインと SSL]** ブレード内からカスタム ドメインを購入したのに、**[管理対象ドメイン]** にカスタム ドメインが表示されない場合は、次を確認してください。

- カスタム ドメインの作成が完了していない場合があります。Azure ポータルの上部にある通知ベルで進捗状況を確認してください。
- 何らかの理由からカスタム ドメインを作成できなかった可能性があります。Azure ポータルの上部にある通知ベルで進捗状況を確認してください。
- カスタム ドメインは作成されましたが、ブレードがまだ更新されていない可能性があります。**[カスタム ドメインと SSL]** ブレードをもう一度開いてください。
- ある時点でカスタム ドメインを削除した可能性があります。アプリのメイン ブレードから **[設定]**、**[監査ログ]** の順にクリックし、監査ログを確認してください。
- 探している **[カスタム ドメインおよび SSL]** ブレードが、別の Azure サブスクリプションで作成されたアプリに属している可能性があります。別のサブスクリプションの別のアプリに切り替え、その **[カスタム ドメインおよび SSL]** ブレードを確認してください。ポータル内では、アプリと異なる Azure サブスクリプションで作成されたカスタム ドメインを表示または管理できません。ただし、ドメインの **[ドメインの管理]** ブレードの **[詳細管理]** をクリックすると、ドメイン プロバイダーの Web サイトにリダイレクトされます。そのサイトで、別の Azure サブスクリプションで作成されたアプリの[カスタム ドメイン (任意の外部カスタム ドメインと同様に) を手動で構成](web-sites-custom-domain-name.md)できます。

<!---HONumber=AcomDC_0921_2016-->