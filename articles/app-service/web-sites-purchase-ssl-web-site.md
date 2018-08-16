---
title: Azure App Service の SSL 証明書を購入して構成する | Microsoft Docs
description: App Service 証明書を購入して App Service アプリにバインドする方法を説明します。
services: app-service
documentationcenter: .net
author: cephalin
manager: cfowler
tags: buy-ssl-certificates
ms.assetid: cdb9719a-c8eb-47e5-817f-e15eaea1f5f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: apurvajo;cephalin
ms.openlocfilehash: 85d0c91a0b1cdf5703b394d6d232ab9cee72ee0c
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39627146"
---
# <a name="buy-and-configure-an-ssl-certificate-for-your-azure-app-service"></a>Azure App Service の SSL 証明書を購入して構成する

このチュートリアルでは、**[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)** の SSL 証明書を購入し、[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) に安全に格納して、カスタム ドメインに関連付けることで、Web アプリをセキュリティで保護する方法について説明します。

## <a name="step-1---sign-in-to-azure"></a>手順 1 - Azure にサインインする

Azure portal (http://portal.azure.com) にサインインする

## <a name="step-2---place-an-ssl-certificate-order"></a>手順 2 - SSL 証明書を注文する

SSL 証明書を注文するには、**Azure Portal** で新しい [App Service 証明書](https://portal.azure.com/#create/Microsoft.SSL)を作成します。

![証明書の作成](./media/app-service-web-purchase-ssl-web-site/createssl.png)

SSL 証明書のフレンドリ**名**と**ドメイン名**を入力します

> [!NOTE]
> この手順は、購入プロセスの最も重要な部分の 1 つです。 この証明書で保護する正しいホスト名 (カスタム ドメイン) を入力してください。 ホスト名の先頭に WWW を**付けないでください**。 
>

**サブスクリプション**、**リソース グループ**、および**証明書の SKU** を選択します

> [!TIP]
> App Service 証明書はあらゆる Azure Service と Azure ではないサービスで利用できます。App Service に限定されません。 そのためには、任意の場所で利用できるように、App Service 証明書のローカル PFX コピーを作成する必要があります。 詳細については、[App Service 証明書のローカル PFX コピーの作成](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/)に関する記事を参照してください。
>

## <a name="step-3---store-the-certificate-in-azure-key-vault"></a>手順 3 - 証明書を Azure Key Vault に保存する

> [!NOTE]
> [Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) は、クラウド アプリケーションやサービスで使用される暗号化キーとシークレットを保護するための Azure サービスです。
>

SSL 証明書の購入が完了したら、[[App Service 証明書]](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) ページを開く必要があります。

![KV に格納する準備完了のイメージを挿入](./media/app-service-web-purchase-ssl-web-site/ReadyKV.png)

この証明書の使用を開始する前に完了する必要のある手順がまだいくつかあるため、証明書の状態は **[発行保留中]** になっています。

[証明書のプロパティ] ページの **[証明書の構成]** をクリックし、**[手順 1: 格納]** をクリックして Azure Key Vault にこの証明書を格納します。

**[Key Vault の状態]** ページの **[Key Vault リポジトリ]** をクリックして、この証明書を格納する既存の Key Vault を選択するか、**[Key Vault の新規作成]** をクリックして同じサブスクリプションとリソース グループに新しい Key Vault を作成します。

> [!NOTE]
> 最小限の料金でこの証明書を Azure Key Vault に格納できます。
> 詳細については、**[Azure Key Vault の価格の詳細](https://azure.microsoft.com/pricing/details/key-vault/)** に関するページをご覧ください。
>

この証明書を格納する Key Vault リポジトリを選択すると、**[格納]** オプションに、成功したことが示されます。

![KV の格納成功のイメージを挿入](./media/app-service-web-purchase-ssl-web-site/KVStoreSuccess.png)

## <a name="step-4---verify-the-domain-ownership"></a>手順 4 - ドメインの所有権を確認する

手順 3 で使用した **[証明書の構成]** ページで、**[手順 2: 確認]** をクリックします。

優先するドメイン確認方法を選択します。 

App Service 証明書では、App Service、ドメイン、および手動の 4 種類のドメイン確認がサポートされています。 これらの確認の種類の詳細については、「[詳細](#advanced)」をご覧ください。

> [!NOTE]
> 確認したいドメインが同一のサブスクリプションで既に App Service アプリにマップされている場合は、**App Service の確認**が最も便利なオプションです。 この方法は、App Service アプリがドメインの所有権を既に確認済みである事実を利用しています。
>

**[確認]** ボタンをクリックして、この手順を実行します。

![ドメイン確認のイメージを挿入](./media/app-service-web-purchase-ssl-web-site/DomainVerificationRequired.png)

**[確認]** をクリックしたら、**[確認]** オプションに成功したことが示されるまで、**[更新]** ボタンを使用します。

![KV の確認成功のイメージを挿入](./media/app-service-web-purchase-ssl-web-site/KVVerifySuccess.png)

## <a name="step-5---assign-certificate-to-app-service-app"></a>手順 5 - App Service アプリに証明書を割り当てる

> [!NOTE]
> このセクションの手順を実行する前に、アプリにカスタム ドメイン名が関連付けられている必要があります。 詳細については、「**[Azure App Service のカスタム ドメイン名の構成](app-service-web-tutorial-custom-domain.md)**」を参照してください。
>

**[Azure Portal](https://portal.azure.com/)** で、ページの左側にある **[App Service]** オプションをクリックします。

この証明書を割り当てるアプリの名前をクリックします。

**[設定]** で、**[SSL 設定]** をクリックします。

**[App Service 証明書のインポート]** をクリックして、購入した証明書を選択します。

![証明書インポートのイメージを挿入](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)

**[SSL のバインディング]** セクションで **[バインディングの追加]** をクリックし、ドロップダウン リストから、SSL でセキュリティ保護するドメイン名、および使用する証明書を選択します。 また、**[Server Name Indication (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication)** または IP ベースの SSL のどちらを使用するかを選択できます。

![SSL バインドのイメージを挿入](./media/app-service-web-purchase-ssl-web-site/SSLBindings.png)

変更を保存して SSL を有効にするには、 **[Add Binding (バインドの追加)]** をクリックします。

> [!NOTE]
> **IP ベースの SSL** を選択し、カスタム ドメインが A レコードを使用して構成されている場合、次の追加の手順を実行する必要があります。 詳細については、「[詳細](#Advanced)」セクションを参照してください。

ここで、証明書が正しく構成されていることを確認するために、`HTTP://` ではなく、`HTTPS://` を使用してアプリを参照できる必要があります。

<!--![insert image of https](./media/app-service-web-purchase-ssl-web-site/Https.png)-->

## <a name="step-6---management-tasks"></a>手順 6 - 管理タスク

### <a name="azure-cli"></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="advanced"></a>詳細

### <a name="verifying-domain-ownership"></a>ドメインの所有権の確認

App Service 証明書では、この他にドメイン確認と手動による確認の 2 種類の確認がサポートされています。

#### <a name="domain-verification"></a>ドメイン確認

[Azure で購入した App Service のドメイン](custom-dns-web-site-buydomains-web-app.md)にのみこのオプションを選択します。 Azure は確認 TXT レコードを自動的に追加し、プロセスを完了します。

#### <a name="manual-verification"></a>手動による確認

> [!IMPORTANT]
> HTML Web ページの確認 (標準の証明書の SKU でのみ動作)
>

1. **"starfield.html"** という名前の HTML ファイルを作成します。

1. このファイルの内容は、ドメイン確認トークンの名前と正確に同じにする必要があります  (トークンは、[ドメインの確認の状態] ページからコピーできます)。

1. ドメイン `/.well-known/pki-validation/starfield.html` をホストする Web サーバーのルートに、このファイルをアップロードします

1. 確認が完了したら、**[更新]** をクリックして証明書の状態を更新します。 検証が完了するまで数分かかる場合があります。

> [!TIP]
> `curl -G http://<domain>/.well-known/pki-validation/starfield.html` を使用してターミナルで確認すると、応答には `<verification-token>` が含まれます。

#### <a name="dns-txt-record-verification"></a>DNS TXT レコード検証

1. DNS マネージャーを使用して、`@` サブドメインに、ドメイン確認トークンと同じ値の TXT レコードを作成します。
1. 確認が完了したら、**[更新]** をクリックして証明書の状態を更新します。

> [!TIP]
> 値 `<verification-token>` で `@.<domain>` に TXT レコードを作成する必要があります。

### <a name="assign-certificate-to-app-service-app"></a>App Service アプリに証明書を割り当てる

**[IP ベースの SSL]** を選択し、カスタム ドメインが A レコードを使用して構成されている場合は、次の追加手順を実行する必要があります。

IP ベースの SSL バインドを構成すると、専用の IP アドレスがアプリに割り当てられます。 この IP アドレスは、アプリの設定の **[カスタム ドメイン]** ページで確認できます。これは、**[ホスト名]** セクションの上にあります。 このアドレスは、**[外部 IP アドレス]** として示されます

![IP SSL のイメージを挿入](./media/app-service-web-purchase-ssl-web-site/virtual-ip-address.png)

この IP アドレスは、ドメイン用の A レコードを構成するために以前使用した仮想 IP アドレスとは異なります。 SNI ベースの SSL を使用するように構成する場合、または SSL を使用するように構成しない場合は、このエントリに対してアドレスは表示されません。

ドメイン名レジストラーから提供されるツールを使用して、前の手順の IP アドレスを指定するようにカスタム ドメイン名用の A レコードを変更します。

## <a name="rekey-and-sync-the-certificate"></a>キーを更新して証明書を同期する

証明書のキーを更新する必要がある場合は、**[証明書のプロパティ]** ページの **[キーの更新と同期]** オプションを選択します。

**[キー更新]** ボタンをクリックして処理を開始します。 処理が完了するまでに 1 ～ 10 分かかることがあります。

![SSL キー更新のイメージを挿入](./media/app-service-web-purchase-ssl-web-site/Rekey.png)

証明書のキーを更新すると、証明機関から発行された新しい証明書が展開されます。

## <a name="renew-the-certificate"></a>証明書を更新する

いつでも、証明書の自動更新を有効にするには、証明書管理ページで **[自動更新の設定]** をクリックします。 **[オン]** を選択して、**[保存]** をクリックします。 自動更新をオンにすると、証明書は有効期限の 90 日前に自動更新を開始できます。

![](./media/app-service-web-purchase-ssl-web-site/auto-renew.png)

代わりに手動で証明書を更新するには、**[手動更新]** をクリックします。 有効期限の 60 日前に、証明書の手動更新を要求できます。

> [!NOTE]
> 手動更新か自動更新かに関係なく、更新された証明書は自動的にはアプリにバインドされません。 アプリにバインドする方法については、「[証明書の更新](./app-service-web-tutorial-custom-ssl.md#renew-certificates)」をご覧ください。 

<a name="notrenewed"></a>
## <a name="why-is-my-certificate-not-auto-renewed"></a>証明書が自動更新されない理由

自動更新するように SSL 証明書が構成されているにもかかわらず、自動的に更新されない場合は、ドメインの確認が保留中になっている可能性があります。 以下の点に注意してください。 

- App Service 証明書を生成する GoDaddy では、2 年に 1 回、ドメインの確認を要求します。 ドメインを確認するために、3 年に 1 回、ドメイン管理者に電子メールが送られます。 この電子メールの確認やドメインの確認を怠ると、App Service 証明書は自動的に更新されなくなります。 
- GoDaddy ポリシーの変更のため、2018 年 3 月 1 日より前に発行された App Service 証明書は、いずれも次回更新時にドメインの再確認が必要となります (証明書の自動更新が有効になっている場合も同様です)。 電子メールを確認し、この 1 回限りのドメインの確認を完了すると、App Service 証明書の自動更新が続行されます。 

## <a name="more-resources"></a>その他のリソース

* [HTTPS の適用](app-service-web-tutorial-custom-ssl.md#enforce-https)
* [TLS 1.1/1.2 の適用](app-service-web-tutorial-custom-ssl.md#enforce-tls-1112)
* [Azure App Service のアプリケーション コードに SSL 証明書を使用する](app-service-web-ssl-cert-load.md)
* [FAQ: App Service 証明書](https://blogs.msdn.microsoft.com/appserviceteam/2017/07/24/faq-app-service-certificates/)
