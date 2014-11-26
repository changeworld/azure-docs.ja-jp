<properties title="Learn how to configure an Azure website to use a domain name registered with Register.com" pageTitle="Configure a Register.com domain name for an Azure website" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

# Azure Website のカスタム ドメイン名の構成 (Register.com)

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/web-sites-custom-domain-name" title="カスタム ドメイン">カスタム ドメイン</a><a href="/ja-jp/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ja-jp/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ja-jp/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com" class="current">Register.com</a><a href="/ja-jp/documentation/articles/web-sites-enom-custom-domain-name" title="Enom" class="current">Enom</a><a href="/ja-jp/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/ja-jp/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/ja-jp/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/ja-jp/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ja-jp/documentation/articles/web-sites-registerdotcom-custom-domain-name/" title="Web サイト" class="current">Web サイト</a> | <a href="/ja-jp/documentation/articles/web-sites-registerdotcom-traffic-manager-custom-domain-name/" title="Traffic Manager を利用する Web サイト">Traffic Manager を利用する Web サイト</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

この記事では、[Register.com][1] から購入したカスタム ドメイン名を Azure Websites で使用する手順を示します。

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

この記事の内容:

-   [DNS レコードについて][DNS レコードについて]
-   [Web サイトの基本、共有、または標準モード用の構成][Web サイトの基本、共有、または標準モード用の構成]
-   [カスタム ドメインの DNS レコードの追加][カスタム ドメインの DNS レコードの追加]
-   [Web サイトでのドメインの有効化][Web サイトでのドメインの有効化]

## <a name="understanding-records"></a>DNS レコードについて

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

## <a name="bkmk_configsharedmode"></a>Web サイトの基本、共有、または標準モード用の構成

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

<a name="bkmk_configurecname"></a>

## カスタム ドメインの DNS レコードの追加

</p>
カスタム ドメインを Azure Websites に関連付けるには、Register.com のツールを使用して、新しいエントリをカスタム ドメインの DNS テーブルに追加する必要があります。次の手順を使用して DNS ツールを見つけて利用します。

1.  Register.com のアカウントにログオンし、右上にある **[Your Account]** を選択してドメインを表示したら、カスタム ドメイン名を選択します。

    ![My Account ページ][My Account ページ]

2.  **[Advanced Technical Settings]** が表示されるまでページを下にスクロールします。このセクションのリンクからドメインのレコードを管理できます。

    -   A レコードの場合、**[Edit IP Address Records]** リンクを使用します。
    -   CNAME レコードの場合、**[Edit Domain Aliases Records]** リンクを使用します。

    ![Advanced Technical Settings ページ][Advanced Technical Settings ページ]

3.  **[Edit]** をクリックすると、既存のレコードの変更や新しいレコードの追加に使用できるフォームが表示されます。このフォームは CNAME レコードと A レコードとで似ています。

    -   CNAME レコードを追加するときは、**[.mydomainname.com]** フィールドを、使用するサブドメインに設定する必要があります。たとえば **www** にします。**[points to]** 値として、Azure Website の **.azurewebsites.net** ドメイン名を選択する必要があります。たとえば **contoso.azurwebsites.net** にします。**[Refers to Host Name]** は **[Select]** のままにします。Azure Websites で使用する CNAME レコードを作成するとき、このフィールドは不要なためです。

        ![CNAME フォーム][CNAME フォーム]

        > [WACOM.NOTE] A レコードを使用する場合、次のいずれかの構成で CNAME レコードを追加する必要があります。
        >
        > -   **[Alias]** 値が **www**、**[Other host]** 値が **\<yourwebsitename\>.azurewebsites.net**。
        >
        > または
        >
        > -   **[Alias]** 値が **awverify.www**、**[Other host]** 値が **awverify.\<yourwebsitename\>.azurewebsites.net**。
        >
        > この CNAME レコードは Azure で使用されて、A レコードで参照されるドメインの所有者が検証されます。

    -   A レコードを追加するときは、**.mydomainname.com** フィールドを、使用するサブドメイン (**www** など) に設定する必要があります。ルート ドメインを設定するには、このフィールドを空白のままにするか、**\*\* を使用してワイルドカード マッピングを作成します。**[points to]\*\* フィールドを、Azure Website の IP アドレスに設定する必要があります。

        ![A レコード フォーム][A レコード フォーム]

4.  レコードの追加または変更が完了したら、**[Continue]** をクリックして変更を確認します。**[Continue]** を再びクリックして変更を保存します。

## <a name="enabledomain"></a>Web サイトでのドメイン名の有効化

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

  [Register.com]: /ja-jp/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com"
  [1]: https://www.register.com
  [DNS レコードについて]: #understanding-records
  [Web サイトの基本、共有、または標準モード用の構成]: #bkmk_configsharedmode
  [カスタム ドメインの DNS レコードの追加]: #bkmk_configurecname
  [Web サイトでのドメインの有効化]: #enabledomain
  [My Account ページ]: ./media/web-sites-custom-domain-name/rdotcom-myaccount.png
  [Advanced Technical Settings ページ]: ./media/web-sites-custom-domain-name/rdotcom-advancedsettings.png
  [CNAME フォーム]: ./media/web-sites-custom-domain-name/rdotcom-editcnamerecord.png
  [A レコード フォーム]: ./media/web-sites-custom-domain-name/rdotcom-editarecord.png
