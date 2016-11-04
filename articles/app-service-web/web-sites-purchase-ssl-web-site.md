---
title: Azure App Service の SSL 証明書を購入して構成する
description: Azure App Service の SSL 証明書を購入して構成する方法を説明します
services: app-service
documentationcenter: .net
author: apurvajo
manager: stefsch
editor: cephalin
tags: buy-ssl-certificates

ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/01/2016
ms.author: apurvajo

---
# Azure App Service の SSL 証明書を購入して構成する
> [!div class="op_single_selector"]
> * [Azure での SSL 証明書の購入](web-sites-purchase-ssl-web-site.md)
> * [別の場所からの SSL 証明書の使用](web-sites-configure-ssl-certificate.md)
> 
> 

**[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)** では、*.azurewebsites.net ドメインのワイルドカード証明書を使用する Web アプリに対して、HTTPS が既定で既に有効になっています。カスタム ドメインを構成する予定がない場合は、既定の HTTPS 証明書を利用できます。ただし、*[他のワイルドカード ドメイン](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates)と同様に、カスタム ドメインに独自の証明書を使用する場合ほど安全ではありません。Azure App Service では、ポータルを離れることなく Azure ポータルから SSL 証明書を購入して管理する簡単な方法が提供されるようになっています。この記事では、3 つの簡単な手順で **[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)** 用の SSL 証明書を購入して構成する方法を説明します。

> [!NOTE]
> カスタム ドメイン名用の SSL 証明書は、無料および共有 Web アプリでは使用できません。Web アプリを Basic モード、Standard モード、または Premium モード用に構成する必要があります。その場合、サブスクリプションに対して課金される金額が変更される可能性があります。詳細については、「**[Web Apps の料金詳細](https://azure.microsoft.com/pricing/details/web-sites/)**」を参照してください。
> 
> 

## <a name="bkmk_Overview"></a>概要
> [!NOTE]
> 有効なクレジット カードが関連付けられていないサブスクリプションを使用して SSL 証明書を購入しないでください。お使いのサブスクリプションが無効になる可能性があります。
> 
> 

## <a>カスタム ドメイン用の SSL 証明書を購入、保存、および割り当てる</a>
contoso.com などのカスタム ドメインに対して HTTPS を有効にするには、まず **[Azure App Service でカスタム ドメイン名を構成する](web-sites-custom-domain-name.md)**必要があります。

SSL 証明書を要求する前に、その証明書により、どのドメイン名をセキュリティで保護するかを最初に決定する必要があります。この結果、どのような種類の証明書を取得する必要があるかが決まります。contoso.com または www.contoso.com のように、ただ 1 つのドメイン名を保護する必要がある場合は、Standard (基本的な) 証明書で十分です。contoso.com、www.contoso.com、および mail.contoso.com のように、複数のドメイン名を保護する必要がある場合は、**[ワイルドカード証明書](http://en.wikipedia.org/wiki/Wildcard_certificate)**を取得できます。

## <a name="bkmk_purchasecert"></a>手順 0: SSL 証明書を注文する
この手順では、選択した SSL 証明書を注文する方法を説明します。

1. **[Azure ポータル](https://portal.azure.com/)**で、[参照] をクリックし、検索バーに「App Service 証明書」と入力し、結果から "App Service 証明書" を選択して、[追加] をクリックします。
   
   ![参照を使用して作成のイメージを挿入](./media/app-service-web-purchase-ssl-web-site/browse.jpg)
   
   ![参照を使用して作成のイメージを挿入](./media/app-service-web-purchase-ssl-web-site/add.jpg)
2. SSL 証明書の**フレンドリ名**を入力します。
3. **[ホスト名]** を入力します。
   
   > [!NOTE]
   > これは、購入プロセスの最も重要な部分の 1 つです。この証明書で保護する正しいホスト名 (カスタム ドメイン) を入力してください。ホスト名には WWW を**付けないでください**。たとえば、カスタム ドメイン名が www.contoso.com の場合、[ホスト名] には contoso.com だけを入力します。購入した証明書では、www ドメインとルート ドメインの両方が保護されます。
   > 
   > 
4. **サブスクリプション**を選択します。
   
   複数のサブスクリプションがある場合は、対象のカスタム ドメインまたは Web アプリに使用したものと同じサブスクリプションで、SSL 証明書を作成してください。
5. **リソース グループ**を選択または作成します。
   
   リソース グループを使用すると、関連する Azure リソースを 1 つの単位として管理でき、アプリ用にロール ベースのアクセス制御 (RBAC) 規則を作成する際に便利です。詳細については、Azure リソースの管理に関するページを参照してください。
6. **[証明書 SKU]** を選択します。
   
   最後に、ニーズに合った証明書 SKU を選択し、[作成] をクリックします。現在、Azure App Service では 2 種類の SKU を購入できます。 • S1 – 有効期間 1 年で自動更新される標準証明書 • W1 – 有効期間 1 年で自動更新されるワイルドカード証明書 詳細については、「**[Web Apps の料金詳細](https://azure.microsoft.com/pricing/details/web-sites/)**」を参照してください。

![証明書 SKU のイメージを挿入](./media/app-service-web-purchase-ssl-web-site/SKU.jpg)

> [!NOTE]
> SSL 証明書の作成には 1 ～ 10 分かかります。このプロセスでは、手作業で行うと非常に面倒な複数の手順がバック グラウンドで実行されます。
> 
> 

## <a name="bkmk_StoreKeyVault"></a>手順 1: 証明書を Azure Key Vault に保存する
この手順では、選択した Azure Key Vault に購入した SSL 証明書を格納する方法を説明します。

1. SSL 証明書の購入が完了した後は、**[App Service 証明書]** リソース ブレードを再度参照して手動で開く必要があります (手順 1 を参照)。
   
   ![KV に格納する準備完了のイメージを挿入](./media/app-service-web-purchase-ssl-web-site/ReadyKV.jpg)
   
   この証明書の使用を開始する前に完了する必要のある手順がまだいくつかあるため、証明書の状態は **[発行保留中]** になっています。
2. [証明書のプロパティ] ブレードの **[証明書の構成]** をクリックし、**[ステップ 1: 格納]** をクリックして Azure Key Vault にこの証明書を格納します。
3. **[Key Vault の状態]** ブレードの **[Key Vault リポジトリ]** をクリックして、この証明書を格納する Key Vault を選択するか、**[Key Vault の新規作成]** をクリックして同じサブスクリプションとリソース グループに新しい Key Vault を作成します。
   
   ![新規 KV 作成のイメージを挿入](./media/app-service-web-purchase-ssl-web-site/NewKV.jpg)
   
   > [!NOTE]
   > 最小限の料金でこの証明書を Azure Key Vault に格納できます。詳細については、「**[Key Vault の価格](https://azure.microsoft.com/pricing/details/key-vault/)**」をご覧ください。
   > 
   > 
4. この証明書を格納する Key Vault リポジトリを選択した後、**[Key Vault の状態]** ブレードの上部にある **[格納]** ボタンをクリックして格納します。
   
    購入した証明書を選択した Azure Key Vault に格納する手順はこれで完了です。ブレードを更新すると、この手順に緑のチェック マークが表示されます。

## <a name="bkmk_VerifyOwnership"></a>手順 2: ドメインの所有権を検証する
この手順では、注文した SSL 証明書のドメイン所有権を検証する方法を説明します。

1. **[証明書の構成]** ブレードで **[ステップ 2: 検証]** をクリックします。App Service 証明書では 4 種類のドメイン検証がサポートされています。
   
   * **App Service 検証**
     
     * これは、既に **App Service アプリにカスタム ドメインを割り当ててある**場合に、最も簡単なプロセスです。 この方法では、この条件を満たすすべての App Service アプリが一覧表示されます。たとえば、この例では、**contosocertdemo.com** は **"ContosoCertDemo"** という名前の App Service アプリに割り当てられているカスタム ドメインであり、ここで表示される唯一の App Service アプリです。複数リージョンのデプロイがあった場合、すべてのリージョンのアプリが一覧表示されます。
       
        この検証方法は、Standard (基本) 証明書購入にのみ使用できます。ワイルドカード証明書の場合は、以下で説明する他の 3 つのオプションを使用してください。
     * **[検証]** ボタンをクリックして、この手順を実行します。
     * 検証が完了した後、**[更新]** をクリックして証明書の状態を更新します。検証が完了するまで数分かかる場合があります。
     
     ![App Service 検証のイメージを挿入](./media/app-service-web-purchase-ssl-web-site/AppVerify.jpg)
   * **ドメイン検証**
     
     * この方法は、**[Azure App Service からカスタム ドメインを購入した](custom-dns-web-site-buydomains-web-app.md)** **場合にのみ**、最も便利な方法です。
     * **[検証]** ボタンをクリックして、この手順を実行します。
     * 検証が完了した後、**[更新]** をクリックして証明書の状態を更新します。検証が完了するまで数分かかる場合があります。
   * **メール検証**
     
     * 検証の電子メールは、このカスタム ドメインに関連付けられているメール アドレスに既に送信されています。
     * 電子メールを開き、検証リンクをクリックして、電子メール検証手順を実行します。
     * 検証メールを再送信する必要がある場合は、**[電子メールを再送信]** ボタンをクリックします。
   * **手動検証**
     
     1. **HTML Web ページ検証**
        
        * **{ドメイン検証トークン}**.html という名前の HTML ファイルを作成します (トークンは [ドメイン検証の状態] ブレードからコピーできます)。
        * このファイルの内容は、**ドメイン検証トークン**の名前と正確に同じにする必要があります。
        * ドメインをホストする Web サーバーのルートに、このファイルをアップロードします。
        * 検証が完了した後、**[更新]** をクリックして証明書の状態を更新します。検証が完了するまで数分かかる場合があります。
          
          たとえば、**‘cAGgQrKc’** というドメイン検証トークンで contosocertdemo.com の Standard 証明書を購入した場合、**‘http://contosocertdemo.com/cAGgQrKc.html’** に対する Web 要求からは **cAGgQrKc** が返ります。
     2. **DNS TXT レコード検証**
        
        * DNS マネージャーを使用して、**"DZC"** サブドメインに、**ドメイン検証トークン**と同じ値の TXT レコードを作成します。
        * 検証が完了した後、**[更新]** をクリックして証明書の状態を更新します。検証が完了するまで数分かかる場合があります。
          
          たとえば、ホスト名が ***.contosocertdemo.com** または ***.subdomain.contosocertdemo.com** でドメイン検証トークンが **cAGgQrKc** であるワイルドカード証明書の検証を実行するには、dzc.contosocertdemo.com に **cAGgQrKc** という値の TXT レコードを作成する必要があります。

## <a name="bkmk_AssignCertificate"></a>手順 3: App Service アプリに証明書を割り当てる
この手順では、この新しく購入した証明書を App Service アプリに割り当てる方法を説明します。

> [!NOTE]
> このセクションの手順を実行する前に、アプリにカスタム ドメイン名が関連付けられている必要があります。詳細については、「**[Azure App Service のカスタム ドメイン名の構成](web-sites-custom-domain-name.md)**」を参照してください。
> 
> 

1. ブラウザーで、**[Azure ポータル](https://portal.azure.com/)**を開きます。
2. ページの左側にある **[App Service]** オプションをクリックします。
3. この証明書を割り当てるアプリの名前をクリックします。
4. **[設定]**で、**[SSL certificates (SSL 証明書)]** をクリックします。
5. **[Import App Service Certificate (App Service 証明書のインポート)]** をクリックして、購入した証明書を選択します。
   
   ![証明書インポートのイメージを挿入](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)
6. **[SSL バインド]** セクションで、**[Add bindings (バインドの追加)]** をクリックします。
7. **[Add SSL Binding (SSL バインドの追加)]** ブレードで、ドロップダウン リストから SSL でセキュリティ保護するドメイン名、および使用する証明書を選択します。また、**[Server Name Indication](http://en.wikipedia.org/wiki/Server_Name_Indication)** (SNI) または IP ベースの SSL のどちらを使用するかを選択できます。
   
    ![SSL バインドのイメージを挿入](./media/app-service-web-purchase-ssl-web-site/SSLBindings.png)
   
       • IP ベースの SSL は、サーバーの専用パブリック IP アドレスをドメイン名にマッピングすることによって、証明書をドメイン名に関連付けします。これは、サービスに関連付けられている各ドメイン名 (contoso.com、fabricam.com など) の専用の IP アドレスが必要となります。これは SSL 証明書と Web サーバーを関連付ける従来の方式です。 • SNI ベースの SSL は、SSL と**[トランスポート層セキュリティ](http://en.wikipedia.org/wiki/Transport_Layer_Security)** (TLS) の拡張機能です。TLS では、複数のドメインが同じ IP アドレスを共有し、各ドメインが独自のセキュリティ証明書を持つことができます。最新のブラウザー (Internet Explorer、Chrome、Firefox、および Opera を含む) のほとんどが SNI をサポートしていますが、古いブラウザーには、SNI をサポートしていないものもあります。SNI の詳細については、Wikipedia の **[Server name Indication](http://en.wikipedia.org/wiki/Server_Name_Indication)** に関する記事を参照してください。
8. 変更を保存して SSL を有効にするには、**[Add Binding (バインドの追加)]** をクリックします。

**[IP ベースの SSL]** を選択し、カスタム ドメインが A レコードを使用して構成されている場合は、次の追加手順を実行する必要があります。

* IP ベースの SSL バインドを構成すると、専用の IP アドレスがアプリに割り当てられます。この IP アドレスは、アプリの設定の **[カスタム ドメイン]** ページで確認できます。これは、**[Hostnames (ホスト名)]** セクションの上にあります。このアドレスは、**[外部 IP アドレス]** として示されます。
  
    ![IP SSL のイメージを挿入](./media/app-service-web-purchase-ssl-web-site/virtual-ip-address.png)
  
    この IP アドレスは、ドメイン用の A レコードを構成するために以前使用した仮想 IP アドレスとは異なります。SNI ベースの SSL を使用するように構成する場合、または SSL を使用するように構成しない場合は、このエントリに対してアドレスは表示されません。

1. ドメイン名レジストラーから提供されるツールを使用して、前の手順の IP アドレスを指定するようにカスタム ドメイン名用の A レコードを変更します。ここで、証明書が正しく構成されていることを確認するために、HTTP:// ではなく、HTTPS:// を使用してアプリを参照することができる必要があります。

## <a name="bkmk_Rekey"></a>キーを更新して証明書を同期する
1. セキュリティ上の理由から証明書のキーを更新する必要がある場合は、**[証明書のプロパティ]** ブレードの **[キーの更新と同期]** オプションを選択します。
2. **[キー更新]** ボタンをクリックして処理を開始します。処理が完了するまでに 1 ～ 10 分かかることがあります。
   
    ![SSL キー更新のイメージを挿入](./media/app-service-web-purchase-ssl-web-site/Rekey.jpg)
3. 証明書のキーを更新すると、証明機関から発行された新しい証明書が展開されます。
4. 証明書の有効期間中は、キーを更新しても課金されません。
5. 証明書のキーを更新すると、発行保留中状態になります。
6. 証明書の準備ができたら、サービスの中断を防ぐため、この証明書を使用してリソースを同期する必要があります。
7. 同期オプションは、Web アプリにまだ割り当てられていない証明書に対しては使用できません。

## その他のリソース
* [アプリに対する HTTPS を Azure App Service で有効にする](web-sites-configure-ssl-certificate.md)
* [Azure App Service でのカスタム ドメイン名の購入と構成](custom-dns-web-site-buydomains-web-app.md)
* [Microsoft Azure のトラスト センター](/support/trust-center/security/)
* [Azure Web Sites でロックを解除された構成オプション](http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)
* [Microsoft Azure 管理ポータル](https://manage.windowsazure.com)

> [!NOTE]
> Azure アカウントにサインアップする前に Azure App Service の使用を開始する場合は、[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)に関するページにアクセスしてください。App Service で有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。
> 
> 

<!---HONumber=AcomDC_0824_2016-->