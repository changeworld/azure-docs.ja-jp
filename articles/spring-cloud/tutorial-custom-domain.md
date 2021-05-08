---
title: 'チュートリアル: 既存のカスタム ドメインを Azure Spring Cloud にマップする'
description: 既存のカスタム Distributed Name Service (DNS) 名を Azure Spring Cloud にマップする方法
author: MikeDodaro
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 7d01f3d5efeee2db5f468a0fe6217d1ff3c313a7
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375017"
---
# <a name="tutorial-map-an-existing-custom-domain-to-azure-spring-cloud"></a>チュートリアル: 既存のカスタム ドメインを Azure Spring Cloud にマップする

**この記事の適用対象:** ✔️ Java ✔️ C#

ドメイン ネーム サービス (DNS) は、ネットワーク全体のネットワーク ノード名を格納するための手法です。 このチュートリアルでは、CNAME レコードを使用して、 www.contoso.com などのドメインをマップします。 証明書を使用してカスタム ドメインをセキュリティで保護し、トランスポート層セキュリティ (TLS。Secure Sockets Layer (SSL) とも呼ばれます) を強制する方法を示します。 

証明書は、Web トラフィックを暗号化します。 これらの TLS/SSL 証明書は、Azure Key Vault に格納できます。 

## <a name="prerequisites"></a>前提条件
* Azure Spring Cloud にデプロイされたアプリケーション (「[クイックスタート: Azure portal を使用して既存の Azure Spring Cloud アプリケーションを起動する](spring-cloud-quickstart.md)」を参照するか、既存のアプリを使用してください)。
* GoDaddy などのドメイン プロバイダーの DNS レジストリへのアクセス権を持つドメイン名。
* サードパーティ プロバイダーからのプライベート証明書 (つまり自己署名証明書)。 証明書はドメインと一致している必要があります。
* [Azure Key Vault](../key-vault/general/overview.md) のデプロイ済みインスタンス

## <a name="keyvault-private-link-considerations"></a>キー コンテナーのプライベート リンクに関する考慮事項

Azure Spring Cloud の管理 IP は、Azure Trusted Microsoft サービスには含まれません。 そのため、プライベート エンドポイント接続によって保護されたキー コンテナーから Azure Spring Cloud が証明書を読み込めるようにするには、Azure Key Vault のファイアウォールに以下の IP を追加する必要があります。

```
20.53.123.160 52.143.241.210 40.65.234.114 52.142.20.14 20.54.40.121 40.80.210.49 52.253.84.152 20.49.137.168 40.74.8.134 51.143.48.243
```

## <a name="import-certificate"></a>証明書のインポート
### <a name="prepare-your-certificate-file-in-pfx-optional"></a>PFX で証明書ファイルを準備する (省略可能)
Azure Key Vault は、PEM および PFX 形式のプライベート証明書のインポートをサポートしています。 証明書プロバイダーから取得した PEM ファイルが次のセクションで動作しない場合: 「[Key Vault に証明書を保存する](#save-certificate-in-key-vault)」、ここの手順に従って Azure Key Vault 用の PFX を生成します。

#### <a name="merge-intermediate-certificates"></a>中間証明書を結合する

証明機関から証明書チェーンの複数の証明書を提供された場合は、それらの証明書を順番に結合する必要があります。

これを行うには、受信した各証明書をテキスト エディターで開きます。

結合した証明書用に _mergedcertificate.crt_ という名前のファイルを作成します。 テキスト エディターで、このファイルに各証明書の内容をコピーします。 証明書の順序は、証明書チェーンの順番に従う必要があります。自分の証明書から始まり、ルート証明書で終わります。 次の例のようになります。

```
-----BEGIN CERTIFICATE-----
<your entire Base64 encoded SSL certificate>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 1>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 2>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded root certificate>
-----END CERTIFICATE-----
```

#### <a name="export-certificate-to-pfx"></a>PFX への証明書のエクスポート

結合した TLS/SSL 証明書を、証明書の要求と一緒に生成された秘密キーと共にエクスポートします。

証明書の要求の生成に OpenSSL を使用した場合、秘密キー ファイルは作成されています。 証明書を PFX にエクスポートするには、次のコマンドを実行します。 プレースホルダーの _&lt;private-key-file>_ と _&lt;merged-certificate-file>_ をそれぞれ、秘密キーのパスとマージされた証明書ファイルに変更します。

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>
```

メッセージが表示されたら、エクスポートのパスワードを定義します。 このパスワードは、後で TLS/SSL 証明書を Azure Key Vault にアップロードするときに使用します。

IIS または _Certreq.exe_ を使用して証明書の要求を生成した場合は、ローカル コンピューターに証明書をインストールした後で [証明書を PFX にエクスポート](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754329(v=ws.11))します。

### <a name="save-certificate-in-key-vault"></a>Key Vault に証明書を保存する
証明書をインポートする手順では、PEM または PFX でエンコードされたファイルがディスク上にあり、秘密キーを持っている必要があります。 
#### <a name="portal"></a>[ポータル](#tab/Azure-portal)
証明書をキー コンテナーにアップロードするには、次の操作を行います。
1. キー コンテナー インスタンスに移動します。
1. 左側のナビゲーション ペインで、 **[証明書]** をクリックします。
1. 上部のメニューで、 **[生成/インポート]** をクリックします。
1. **[証明書の作成]** ダイアログの **[証明書の作成方法]** で、[`Import`] を選択します。
1. **[証明書ファイルのアップロード]** で、証明書の場所に移動し、選択します。
1. **[パスワード]** で、証明書の秘密キーを入力します。
1. **Create** をクリックしてください。

    ![証明書のインポート 1](./media/custom-dns-tutorial/import-certificate-a.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

```azurecli
az keyvault certificate import --file <path to .pfx file> --name <certificate name> --vault-name <key vault name> --password <export password>
```
---

### <a name="grant-azure-spring-cloud-access-to-your-key-vault"></a>キー コンテナーへの Azure Spring Cloud のアクセス権を付与する

証明書をインポートする前に、次のようにして Azure Spring Cloud にキー コンテナーへのアクセス権を付与する必要があります。
#### <a name="portal"></a>[ポータル](#tab/Azure-portal)
1. キー コンテナー インスタンスに移動します。
1. 左側のナビゲーション ペインで、 **[アクセス ポリシー]** をクリックします。
1. 上部のメニューで、 **[アクセス ポリシーの追加]** をクリックします。
1. 情報を入力し、 **[追加]** 、 **[保存]** の順にクリックしてアクセス ポリシーを保存します。

| Secret permission (シークレットのアクセス許可) | 証明書の権限 | プリンシパルの選択 |
|--|--|--|
| Get、List | Get、List | Azure Spring cloud Domain-Management (Azure Spring Cloud ドメイン - 管理) |

![証明書のインポート 2](./media/custom-dns-tutorial/import-certificate-b.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

Azure Spring Cloud にキー コンテナーへの読み取りアクセス権を付与し、次のコマンドの `<key vault resource group>` と `<key vault name>` を置き換えます。
```
az keyvault set-policy -g <key vault resource group> -n <key vault name>  --object-id 938df8e2-2b9d-40b1-940c-c75c33494239 --certificate-permissions get list --secret-permissions get list
``` 
---

### <a name="import-certificate-to-azure-spring-cloud"></a>Azure Spring Cloud に証明書をインポートする
#### <a name="portal"></a>[ポータル](#tab/Azure-portal)
1. サービス インスタンスに移動します。 
1. アプリの左側のナビゲーション ペインで、 **[TLS/SSL の設定]** を選択します。
1. その後、 **[Key Vault 証明書のインポート]** をクリックします。

    ![証明書のインポート](./media/custom-dns-tutorial/import-certificate.png)

1. 証明書が正常にインポートされると、 **[秘密キー証明書]** の一覧に表示されます。

    ![秘密キー証明書](./media/custom-dns-tutorial/key-certificates.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

```
az spring-cloud certificate add --name <cert name> --vault-uri <key vault uri> --vault-certificate-name <key vault cert name>
```

インポートされた証明書の一覧を表示するには

```
az spring-cloud certificate list --resource-group <resource group name> --service <service name>
```
---

> [!IMPORTANT] 
> この証明書を使用してカスタム ドメインをセキュリティで保護するには、証明書を特定のドメインにバインドする必要があります。 次のセクションの手順に従います: 「[SSL バインドの追加](#add-ssl-binding)」。

## <a name="add-custom-domain"></a>カスタム ドメインの追加
カスタム DNS 名を Azure Spring Cloud にマップするには、CNAME レコードを使用できます。 

> [!NOTE] 
> A レコードはサポートされていません。 

### <a name="create-the-cname-record"></a>CNAME レコードを作成する
DNS プロバイダーに移動し、CNAME レコードを追加して、ドメインを <service_name>.azuremicroservices.io にマップします。 ここで、<service_name> は Azure Spring Cloud インスタンスの名前です。 ワイルドカード ドメインとサブ ドメインがサポートされています。 CNAME を追加した後の DNS レコード ページは、次の例のようになります。 

![DNS レコード ページ](./media/custom-dns-tutorial/dns-records.png)

## <a name="map-your-custom-domain-to-azure-spring-cloud-app"></a>Azure Spring Cloud アプリへのカスタム ドメインのマップ
Azure Spring Cloud にアプリケーションがない場合は、「[クイックスタート: Azure portal を使用して既存の Azure Spring Cloud アプリケーションを起動する](./spring-cloud-quickstart.md)」の手順に従ってください。

#### <a name="portal"></a>[ポータル](#tab/Azure-portal)
アプリケーション ページに移動します。

1. **[カスタム ドメイン]** を選択します。
2. 次に、 **[カスタム ドメインの追加]** を選択します。 

    ![カスタム ドメイン](./media/custom-dns-tutorial/custom-domain.png)

3. CNAME レコードを追加した完全修飾ドメイン名 (www.contoso.com など) を入力します。 ホスト名レコード タイプが CNAME に設定されていることを確認します (<service_name>.azuremicroservices.io)
4. **[検証]** をクリックすると、 **[追加]** ボタンが有効になります。
5. **[追加]** をクリックします。

    ![カスタム ドメインの追加](./media/custom-dns-tutorial/add-custom-domain.png)

1 つのアプリは複数のドメインを持つことができますが、1 つのドメインは 1 つのアプリにのみマップできます。 カスタムド メインがアプリに正常にマップされると、カスタム ドメイン テーブルにそのドメインが表示されます。

![カスタム ドメイン テーブル](./media/custom-dns-tutorial/custom-domain-table.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)
```
az spring-cloud app custom-domain bind --domain-name <domain name> --app <app name> --resource-group <resource group name> --service <service name>
```

カスタム ドメインの一覧を表示するには
```
az spring-cloud app custom-domain list --app <app name> --resource-group <resource group name> --service <service name>
```
---

> [!NOTE]
> カスタム ドメインの **[セキュリティ保護なし]** というラベルは、まだ SSL 証明書にバインドされていないことを意味します。 ブラウザーからカスタム ドメインへの HTTPS 要求を実行すると、エラーまたは警告が表示されます。

## <a name="add-ssl-binding"></a>SSL バインドの追加

#### <a name="portal"></a>[ポータル](#tab/Azure-portal)
前の図に示されているように、カスタム ドメイン テーブルで **[SSL バインディングの追加]** を選択します。  
1. **[証明書]** で選択するか、証明書をインポートします。
1. **[保存]** をクリックします。

    ![SSL バインディングの追加 1](./media/custom-dns-tutorial/add-ssl-binding.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)
```
az spring-cloud app custom-domain update --domain-name <domain name> --certificate <cert name> --app <app name> --resource-group <resource group name> --service <service name>
```
---

SSL バインディングが正常に追加されると、ドメインの状態は安全になり、 **[正常]** と表示されます。 

![SSL バインディングの追加 2](./media/custom-dns-tutorial/secured-domain-state.png)

## <a name="enforce-https"></a>HTTPS の適用
既定では、ユーザーは引き続き HTTP を使用してアプリにアクセスできますが、管理者はすべての HTTP 要求を HTTPS ポートにリダイレクトすることができます。
#### <a name="portal"></a>[ポータル](#tab/Azure-portal)
アプリ ページで、左側のナビゲーションにある **[カスタム ドメイン]** を選択します。 次に、 **[HTTPS のみ]** を *True* に設定します。

![SSL バインディングの追加 3](./media/custom-dns-tutorial/enforce-http.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)
```
az spring-cloud app update -n <app name> --resource-group <resource group name> --service <service name> --https-only
```
---
操作が完了したら、アプリを指す HTTPS URL のいずれかに移動します。 HTTP URL では機能しないことに注意してください。

## <a name="see-also"></a>関連項目
* [Azure Key Vault とは](../key-vault/general/overview.md)
* [証明書のインポート](../key-vault/certificates/certificate-scenarios.md#import-a-certificate)
* [Azure CLI を使用して Spring Cloud アプリを起動する](./spring-cloud-quickstart.md)
