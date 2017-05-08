---
title: "Azure App Service アプリに SSL 証明書を追加する |Microsoft Docs"
description: "SSL 証明書を App Service アプリに追加する方法について説明します。"
services: app-service
documentationcenter: .net
author: ahmedelnably
manager: stefsch
editor: cephalin
tags: buy-ssl-certificates
ms.assetid: cdb9719a-c8eb-47e5-817f-e15eaea1f5f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2016
ms.author: apurvajo;aelnably
translationtype: Human Translation
ms.sourcegitcommit: 0b53a5ab59779dc16825887b3c970927f1f30821
ms.openlocfilehash: 00e252e249dbd1a38a4649e435071685860722e4
ms.lasthandoff: 04/07/2017


---

# <a name="add-an-ssl-certificate-to-your-app-service-app"></a>App Service アプリに SSL 証明書を追加する
> [!div class="op_single_selector"]
> * [Azure で SSL 証明書を購入する](web-sites-purchase-ssl-web-site.md)
> * [他の場所で取得した SSL 証明書を使用する](web-sites-configure-ssl-certificate.md)
> 
> 

[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) では、\*.azurewebsites.net ドメインのワイルドカード証明書を使用する Web アプリに対して、HTTPS が既定で有効になっています。 カスタム ドメインをセットアップする予定がない場合は、既定の HTTPS 証明書を利用できます。 ただし、すべての[ワイルドカード ドメイン](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates)と同様に、Azure のワイルドカード証明書は、カスタム ドメインに独自の証明書を使用する場合ほど安全ではありません。

App Service には、Azure Portal で SSL 証明書を購入して管理する簡単な方法が用意されています。 

この記事では、[App Service](http://go.microsoft.com/fwlink/?LinkId=529714) アプリ用の SSL 証明書を購入してセットアップする方法を説明します。 

> [!NOTE]
> Free または Shared の App Service プランでホストされているアプリに対してカスタム ドメイン名の SSL 証明書を使用することはできません。 SSL 証明書を使用するには、Web アプリが Basic、Standard、Premium のいずれかの App Service プランでホストされている必要があります。 なお、サブスクリプション タイプを変更すると、サブスクリプションに課金される金額が変わる場合があります。 詳細については、「[App Service の価格](https://azure.microsoft.com/pricing/details/web-sites/)」を参照してください。
> 
> 

> [!WARNING]
> SSL 証明書を購入する際に、有効なクレジット カードが関連付けられていないサブスクリプションを使用しないでください。 お使いのサブスクリプションが無効になる可能性があります。 
> 
> 

## <a name="prerequisites"></a>前提条件
カスタム ドメインに対して HTTPS を有効にするには、まず [Azure アプリにカスタム ドメイン名をマップする](web-sites-custom-domain-name.md)必要があります。

SSL 証明書を要求する前に、その証明書を使用してどのドメイン名を保護するかを決定してください。 これによって、必要な証明書のタイプが決まります。 contoso.com *または* www.contoso.com のように、1 つのドメイン名を保護する必要がある場合は、標準 (基本的な) 証明書で十分です。 contoso.com、www.contoso.com、*および* mail.contoso.com のように、複数のドメイン名を保護する必要がある場合は、[ワイルドカード証明書](http://en.wikipedia.org/wiki/Wildcard_certificate)を取得できます。

## <a name="bkmk_purchasecert"></a>SSL 証明書を購入する

1. [Azure Portal](https://portal.azure.com/) のメニューで **[参照]** を選択します。 **検索**ボックスに、「**App Service 証明書**」と入力します。 検索結果で **[App Service 証明書]** を選択します。 

   ![参照を使用して作成する](./media/app-service-web-purchase-ssl-web-site/browse.jpg)
   
2. **[App Service 証明書]** ページで **[追加]** を選択します。 

   ![証明書を追加する](./media/app-service-web-purchase-ssl-web-site/add.jpg)

3. SSL 証明書の**名前**を入力します。
4. **ホスト名**を入力します。
   
   > [!WARNING]
   > これは、購入プロセスの最も重要な部分の 1 つです。 必ず、この証明書で保護するホスト名 (カスタム ドメイン名) を正しく入力してください。 ホスト名の先頭に "www" を*追加しないでください*。 たとえば、カスタム ドメイン名が www.contoso.com の場合、**[ホスト名]** ボックスには **contoso.com** だけを入力します。 これで、証明書によって www ドメインとルート ドメインの両方が保護されます。 
   > 

5. **サブスクリプション**を選択します。 
   
   複数のサブスクリプションがある場合は、対象のカスタム ドメインまたは Web アプリに使用したものと同じサブスクリプションで、SSL 証明書を作成してください。

6. **リソース グループ**を選択または作成します。
   
   リソース グループを使用すると、関連する複数の Azure リソースを 1 つの単位として管理できます。 アプリ用にロール ベースのアクセス制御 (RBAC) 規則を作成する際に便利です。 詳細については、Azure リソースの管理に関するページを参照してください。

7. **[証明書 SKU]** を選択します。 
   
   ニーズに合った証明書 SKU を選択し、**[作成]** をクリックします。 
   
   App Service では、次の 2 つの SKU から選択できます。
   * **S1**: 有効期間が 1 年で、自動更新が設定された標準証明書  
   * **W1**: 有効期間が 1 年で、自動更新が設定されたワイルドカード証明書       
  
    ![証明書 SKU](./media/app-service-web-purchase-ssl-web-site/SKU.jpg)

    詳細については、「[App Service の価格](https://azure.microsoft.com/pricing/details/web-sites/)」を参照してください。

> [!NOTE]
> SSL 証明書の作成には、最大で 10 分かかる場合があります。 このプロセスでは、複数のステップがバック グラウンドで実行されます。  
> 
> 

## <a name="bkmk_StoreKeyVault"></a>証明書を Azure Key Vault に格納する

1. SSL 証明書の購入が完了したら、Azure Portal の **[App Service 証明書]** ブレードに移動します。

   ![Key Vault に格納する準備ができた証明書](./media/app-service-web-purchase-ssl-web-site/ReadyKV.jpg)
   
   証明書の状態は "**発行の保留中**" になっています。 この証明書を使用するには、事前にいくつかの手順を完了する必要があります。

2. **[証明書のプロパティ]** ブレードで、**[証明書の構成]** を選択します。 Key Vault にこの証明書を格納するには、**[手順 1: 格納]** を選択します。
3. この証明書の格納先として既存の Key Vault を選択する場合は、**[Key Vault の状態]** ブレードで、**[Key Vault リポジトリ]** を選択します。  同じサブスクリプションおよびリソース グループで新しい Key Vault を作成する場合は、**[新しい Key Vault の作成]** を選択します。

   ![新しい Key Vault を作成する](./media/app-service-web-purchase-ssl-web-site/NewKV.jpg)
   
   > [!NOTE]
   > Azure Key Vault では、最小限の料金で証明書を格納できます。 詳細については、「[Key Vault の価格](https://azure.microsoft.com/pricing/details/key-vault/)」を参照してください。
   > 
   > 

4. この証明書を格納する Key Vault リポジトリを選択したら、**[Key Vault の状態]** ブレードの上部にある **[格納]** ボタンをクリックします。  
   
選択内容を確認するには、ブラウザーの [更新] ボタンをクリックします。 緑色のチェック マークが付いていれば、この手順が完了したことを示しています。

## <a name="bkmk_VerifyOwnership"></a>ドメインの所有権を確認する

1. **[証明書の構成]** ブレードで **[手順 2: 確認]** を選択します。
2. 次の情報に基づいて、確認オプションを選択します。 

App Service 証明書では、次の 3 種類のドメイン確認がサポートされています。

   * ドメイン確認
   * メールによる確認
   * 手動による確認

### <a name="domain-verification"></a>ドメイン確認 
     
ドメイン確認は、最も便利な方法ですが、[Azure App Service からカスタム ドメインを購入した](custom-dns-web-site-buydomains-web-app.md)場合に*のみ*利用できます。

1. この手順を行うには、**[確認]** を選択します。
2. 確認が完了したら、**[更新]** を選択して証明書の状態を更新します。 確認が完了するまで数分かかる場合があります。

### <a name="mail-verification"></a>メールによる確認
     
カスタム ドメインでは、このドメインに関連付けられているメール アドレスに確認メールが送信されます。 

1. メールによる確認の手順を実行する場合は、この確認メールを開き、確認リンクをクリックします。 
2. 確認メールを再送信する必要がある場合は、**[メールの再送信]** ボタンをクリックします。

### <a name="manual-verification"></a>手動による確認    
     
**HTML Web ページの確認 (標準証明書 SKU でのみ可能)**

1. starfield.html という名前の HTML ファイルを作成します。 このファイルの内容は、ドメイン確認トークンの名前と正確に同じにする必要があります  (**[ドメインの確認の状態]** ブレードからトークンをコピーできます)。
2. ドメインをホストしている Web サーバーのルートに、このファイルをアップロードします  (例: /.well-known/pki-validation/starfield.html)。
3.  確認が完了したら、**[更新]** を選択して証明書の状態を更新します。 確認が完了するまで数分かかる場合があります。

    たとえば、**tgjgthq8d11ttaeah97s3fr2sh** というドメイン確認トークンで **contosocertdemo.com** の標準証明書を購入した場合、**http://contosocertdemo.com/.well-known/pki-validation/starfield.html** に対する Web 要求に対して **tgjgthq8d11ttaeah97s3fr2sh** が返されます。

**DNS TXT レコード検証**
        
1. DNS マネージャーを使用して、**@** サブドメインに、**ドメイン確認トークン**と同じ値の TXT レコードを作成します。
2. 確認が完了したら、**[更新]** を選択して証明書の状態を更新します。 確認が完了するまで数分かかる場合があります。
 
   たとえば、ホスト名が **\*.contosocertdemo.com** または **\*.subdomain.contosocertdemo.com** でドメイン確認トークンが **tgjgthq8d11ttaeah97s3fr2sh** であるワイルドカード証明書の確認を実行するには、**contosocertdemo.com** に **tgjgthq8d11ttaeah97s3fr2sh** という値の TXT レコードを作成します。     

## <a name="bkmk_AssignCertificate"></a>App Service アプリに証明書を割り当てる

> [!NOTE]
> このセクションの手順を実行する前に、アプリにカスタム ドメイン名を関連付ける必要があります。 詳細については、[Web アプリのカスタム ドメイン名の構成](web-sites-custom-domain-name.md)に関するページを参照してください。
> 
> 

1. [Azure Portal](https://portal.azure.com/) のメニューで **[App Service]** を選択します。
2. この証明書を割り当てるアプリの名前を選択します。 
3. **[設定]** > **[SSL 証明書]** > **[App Service 証明書のインポート]** の順に移動して、証明書を選択します。

   ![証明書のインポート](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)

4. **[SSL バインド]** セクションで、**[バインディングの追加]** を選択します。
5. **[SSL バインディングの追加]** ブレードで、SSL 証明書でセキュリティ保護するドメイン名を選択します。 使用する証明書を選択します。 [Server Name Indication (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication) ベースと IP ベースのどちらの SSL を使用するかも選択できます。

   ![SSL バインディング](./media/app-service-web-purchase-ssl-web-site/SSLBindings.png)
   
    * IP ベースの SSL では、証明書をドメイン名に関連付けるために、サーバーの専用パブリック IP アドレスをドメイン名にマップします。 IP ベースの SSL を使用する場合は、サービスに関連付けられている各ドメイン名 (contoso.com、fabricam.com など) に専用の IP アドレスが必要になります。 これは SSL 証明書と Web サーバーを関連付けるための従来の方式です。
    * SNI ベースの SSL は、SSL および[トランスポート層セキュリティ (TLS)](http://en.wikipedia.org/wiki/Transport_Layer_Security) の拡張機能です。 SNI ベースの SSL を使用すると、複数のドメインで同じ IP アドレスを共有できます。 セキュリティ証明書はドメインごとに用意します。 最新のブラウザー (Internet Explorer、Chrome、Firefox、Opera を含む) のほとんどが SNI をサポートしています。 古いブラウザーには、SNI をサポートしていないものもあります。 SNI の詳細については、Wikipedia の [Server Name Indication](http://en.wikipedia.org/wiki/Server_Name_Indication) に関する記事を参照してください。

6. **[バインディングの追加]** をクリックすると、変更が保存されて SSL が有効になります。

**[IP ベースの SSL]** を選択し、カスタム ドメインが A レコードを使用して構成されている場合は、次の追加手順を実行する必要があります。

1.  IP ベースの SSL バインドをセットアップすると、専用の IP アドレスがアプリに割り当てられます。 この IP アドレスを確認するには、**[設定]** > **[カスタム ドメイン]** の順に移動します。 **[ホスト名]** セクションのすぐ上に、IP アドレスが**外部 IP アドレス**として表示されます。

   ![IP ベースの SSL](./media/app-service-web-purchase-ssl-web-site/virtual-ip-address.png)
    
  この IP アドレスは、前にドメイン用の A レコードを構成するために使用した仮想 IP アドレスとは異なります。 アプリが SNI ベースの SSL を使用するよう設定されている場合と SSL を使用するよう設定されていない場合は、ここに IP アドレスは表示されません。

2.  ドメイン名レジストラーから提供されるツールを使用して、前の手順の IP アドレスを指定するようにカスタム ドメイン名用の A レコードを変更します。

3.  証明書が正しく構成されていることを確認するために、HTTP:// ではなく、HTTPS:// を使用してアプリにアクセスします。

## <a name="bkmk_Export"></a>App Service 証明書をエクスポートする
App Service 証明書のローカル PFX コピーを作成することができます。 ローカル コピーがあれば、他の Azure サービスでも証明書を使用できます。 詳細については、[App Service 証明書のローカル PFX コピーの作成](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/)に関するブログ記事を参照してください。

## <a name="bkmk_Renew"></a>App Service 証明書を自動更新する
証明書に自動更新を設定したり、証明書を手動で更新したりするには、**[証明書のプロパティ]** ブレードで **[自動更新の設定]** を選択します。 

![自動更新の設定](./media/app-service-web-purchase-ssl-web-site/autorenew.png)

期限切れになる前に証明書を自動的に更新するには、**[自動更新]** を **[オン]** に設定します。 これが既定のオプションです。 自動更新がオンになっている場合、90 日目以降、期限切れになる前に証明書の更新が試みられます。 Azure Portal で App Service アプリに SSL バインディングを作成した場合は、(キー更新や同期のシナリオと同様に) 準備が整ったら新しい証明書と共にこれらのバインドも更新されます。 

更新を手動で処理する場合は、**[自動更新]** を **[オフ]** に設定します。 App Service 証明書は、有効期限が 90 日以内の場合のみ手動で更新できます。

## <a name="bkmk_Rekey"></a>キーを更新して証明書を同期する

1. (セキュリティ上の理由から) 証明書のキーを更新する必要がある場合は、**[証明書のプロパティ]** ブレードで **[キー更新と同期]** を選択します。 
2. **[キー更新]** を選択します。 このプロセスは完了するまで最大で 10 分かかる場合があります。 

   ![SSL のキー更新](./media/app-service-web-purchase-ssl-web-site/Rekey.jpg)

キー更新に関する次の追加情報もご確認ください。

* 証明書のキー更新を実行すると、証明書は新しい証明書でロールされます。 新しい証明書は証明機関から発行されます。
* 証明書の有効期間中は、キーを更新しても課金されません。 
* キーの更新中、証明書の状態は "**発行の保留中**" になります。 
* 証明書の準備が完了したら、サービスが中断されないように、必ずこの証明書を使ってリソースを同期してください。
* 同期オプションは、Web アプリにまだ割り当てられていない証明書に対しては使用できません。 

## <a name="next-steps"></a>次のステップ

* [HTTPS を使用したアプリのカスタム ドメインのセキュリティ保護](web-sites-configure-ssl-certificate.md)
* [Azure App Service でのカスタム ドメイン名の購入と構成](custom-dns-web-site-buydomains-web-app.md)
* [Microsoft Azure のトラスト センター](https://azure.microsoft.com/en-us/support/trust-center/)

> [!NOTE]
> Azure アカウントにサインアップする前に Azure App Service を使用する方法については、「[Azure App Service を試す](https://azure.microsoft.com/try/app-service/)」を参照してください。 App Service で短時間有効な簡易版の Web アプリを作成できます。 試用にあたり、クレジット カードや契約は必要ありません。
> 
> 

